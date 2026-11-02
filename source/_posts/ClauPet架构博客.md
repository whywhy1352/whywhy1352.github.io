# 拆解我的桌宠 ClauPet：一条消息是怎么让宠物换表情的

> ClauPet 是一只住在 Windows 桌面上的小宠物，它会根据 Claude Code 正在做什么切换表情：你发消息时它开始工作，Claude 等你授权时它举手，出错了它会着急。
>
> 项目地址：https://github.com/whywhy1352/ClauPet
>
> 这个项目的架构是我和 Claude 一起讨论出来的，代码是我自己一行行写的。写这篇文章，是想确认我真的理解了每个设计背后的"为什么"。读这篇文章只需要 C++ 基础，不需要 Qt 经验。

---

## 一张图看懂整体结构

```
┌──────────────┐  HTTP POST（JSON）   ┌──────────────────────────────────────────┐
│ Claude Code  │ ───────────────────▶ │ ClauPet（Petwindow）                      │
│ （hooks 配置）│ ◀─────────────────── │                                          │
└──────────────┘     200 OK          │  QTcpServer ─▶ tryReadBody ─▶ handleHookEvent
                                     │                                │          │
                                     │      m_sessionTitles ◀─────────┤          │
                                     │      m_sessionStates ◀─────────┘          │
                                     │             │                             │
                                     │       combinedState()                     │
                                     │             ▼                             │
   鼠标悬停 / 拖动 / 单击 ───────────▶│  setState() / m_dragging / 闹钟           │
                                     │             │  update()（预约重画）        │
                                     │             ▼                             │
                                     │        paintEvent()  ──▶ 屏幕上的宠物      │
                                     └──────────────────────────────────────────┘
```

整个程序只有一个核心类 `Petwindow`（继承自 Qt 的 `QWidget`），加上一个 `main.cpp`。技术栈是 C++ + Qt 6 + CMake。

下面我按"程序从启动到退出"的顺序，把每一步走一遍。

---

## 最重要的一个概念：登记，和执行

在讲具体流程之前，先讲一个贯穿全文的概念。不理解它，后面每一步都会看糊涂。

看这段代码：

```cpp
connect(&m_server, &QTcpServer::newConnection, this, [this]()
	{
		// 处理新连接……
	});
```

执行到这一句时，lambda 里面的代码**一行都没有执行**。`connect` 只是告诉 Qt："**以后**有新连接进来时，执行这段代码。"我把这叫作"**登记**"。

那登记的代码什么时候执行？答案在 `main` 的最后一行：

```cpp
int main(int argc, char* argv[])
{
	QApplication app(argc, argv);
	app.setQuitOnLastWindowClosed(false);	// 宠物隐藏后程序不退出，留在托盘里
	Petwindow pet{};						// 构造函数里做完所有"登记"
	// ……检查端口，见后文……
	pet.show();								// 只是告诉 Qt"要显示"，这时还没画出来
	return app.exec();						// 进入事件循环，程序真正开始运行
}
```

`exec()` 会进入**事件循环**：不停地等待事件（新连接、鼠标移动、定时器到点、窗口需要重画……），来一个，就去执行当初为它登记的代码，执行完再回来继续等，直到有人调用 `quit()`。

打个比方：构造函数是餐厅**开门前**写好规章制度（"客人来了怎么接待""电话响了谁去接"），`exec()` 是**开门营业**，按规章处理一件件发生的事。

记住这一点：**程序大部分时间都在事件循环里"等"，有事发生才执行对应的代码。**

---

## 第零幕：启动

### 构造函数只当"目录"

```cpp
Petwindow::Petwindow()
{
	setUpWindow();		// 窗口大小、无边框、置顶、不在任务栏显示、背景透明
	setUpAnimation();	// 准备浮动动画的"闹钟"和"秒表"
	restorePosition();	// 恢复上次保存的位置
	loadImages();		// 加载 5 张宠物图片
	setUpServer();		// 开始监听端口，登记处理请求的代码
	setUpTrayMenu();	// 创建右键菜单和托盘图标
}
```

构造函数本身只有 6 行，每一步的细节都放在单独的私有函数里。读代码的人一眼就能看出启动时做了哪几件事。

这 6 步里只有一个顺序要求：**`loadImages` 必须在 `setUpTrayMenu` 之前**，因为托盘图标用的就是"空闲"那张图。反过来的话，托盘里会是一个空白图标。其他几步互不影响。

几个值得一提的细节：

- **窗口比宠物高**：宠物是 180×180，但窗口上下各多留了浮动的幅度，顶部还多留了气泡的高度。背景是透明的，多出来的地方看不见，点击也会穿透过去。
- **恢复位置时会检查屏幕**：上次如果把宠物拖到了副屏，这次副屏没接，就不恢复，否则宠物会出现在看不见的地方。

```cpp
if (QGuiApplication::screenAt(savedPos + QPoint(kPetSize / 2, kPetSize / 2)) != nullptr)
{
	move(savedPos);
}
```

### 端口兼当"防重复启动的锁"

如果不小心又双击了一次 ClauPet.exe 会怎样？

第二个进程会走一遍**完全一样**的构造流程，直到 `setUpServer` 里的 `listen` 失败：端口已经被第一个进程占着了。回到 `main`，它发现自己没在监听，就弹窗提示并退出：

```cpp
if (pet.isListening())
{
	pet.show();
}
else
{
	QMessageBox::warning(nullptr, "Error", message);	// 端口被占用，可能已经在运行了
	return 1;	// 直接返回，不进入事件循环
}
```

程序里并没有专门写"检查是不是已经在运行"的代码，而是借用了"**一个端口只能被一个程序监听**"这个特性。一举两得。

---

## 第一幕：一条消息的旅程

### Claude Code 怎么通知 ClauPet

Claude Code 支持 **hooks**：在特定事件发生时，执行一个命令或者发一个 HTTP 请求。ClauPet 用的是 HTTP 方式，在 `~/.claude/settings.json` 里配置：

```json
{
  "hooks": {
    "UserPromptSubmit": [
      { "hooks": [ { "type": "http", "url": "http://127.0.0.1:52790/", "timeout": 2 } ] }
    ]
  }
}
```

（实际配置了 7 个事件：`UserPromptSubmit`、`PreToolUse`、`PostToolUse`、`PermissionRequest`、`Stop`、`StopFailure`、`SessionEnd`，格式都一样。）

当你在 Claude Code 里发了一条消息，ClauPet 就会收到这样一个请求：

```
POST / HTTP/1.1
Content-Type: application/json
Content-Length: 97

{"hook_event_name":"UserPromptSubmit","session_id":"aaa","session_title":"修复登录 bug"}
```

### 第一站：两层 lambda

请求到来时，执行的**不是** `setUpServer`（它在启动时就执行完了），而是它当时登记的两个 lambda：

```cpp
connect(&m_server, &QTcpServer::newConnection, this, [this]()
	{
		QTcpSocket* socket{ m_server.nextPendingConnection() };
		connect(socket, &QTcpSocket::readyRead, this, [socket, this]()
			{
				QByteArray body{};
				if (!tryReadBody(socket, body))
				{
					return;	// 还没收齐：等下一次 readyRead
				}
				handleHookEvent(body);
				socket->write("HTTP/1.1 200 OK\r\nContent-Length: 0\r\nConnection: close\r\n\r\n");
				socket->disconnectFromHost();
			});
		connect(socket, &QTcpSocket::disconnected, socket, &QObject::deleteLater);
	});
```

- 有新连接时，第一层 lambda 执行，它为这条连接**再登记**"收到数据时做什么"
- 数据到了，第二层 lambda 执行：读数据、处理、回复
- 连接断开后，`deleteLater` 在安全的时机删掉这个 socket

**为什么一定要回复？** Claude Code 发完请求会等回复。hook 配置了 `timeout: 2`，不回复的话每个事件都要白等 2 秒，Claude Code 就变慢了。反过来，这个超时也是一层保险：就算 ClauPet 卡住或者没开，Claude Code 最多受影响 2 秒。

### 第二站：tryReadBody，数据收齐了吗？

TCP 传的是"一串字节"，**不保证一次就全部送到**。一个请求可能分好几次到达，`readyRead` 也就会触发好几次。所以每次都要先判断：收齐了没有？

```cpp
static bool tryReadBody(QTcpSocket* socket, QByteArray& body)
{
	const QByteArray data{ socket->peek(socket->bytesAvailable()) };	// 只"偷看"，不取走
	const qsizetype headerEnd{ data.indexOf("\r\n\r\n") };	// 请求头结束的位置
	if (headerEnd == -1)
	{
		return false;	// 请求头还没收完
	}
	// ……从请求头里找出 Content-Length（先转成小写再找）……
	const qsizetype bodyStart{ headerEnd + 4 };
	if (data.size() < bodyStart + contentLength)
	{
		return false;	// 请求体还没收齐
	}
	socket->readAll();	// 收齐了：这时才真正取走
	body = data.mid(bodyStart, contentLength);
	return true;
}
```

几个关键点：

- **判断收齐的依据**：先找空行 `\r\n\r\n`（请求头到此结束），再看请求头里的 `Content-Length` 说请求体有多长。
- **`peek` 和 `readAll` 的分工**：没收齐时如果用 `readAll` 把数据取走，前半截就丢了，下次永远拼不完整。所以没收齐时只"偷看"，数据留在 socket 里，等下次连同新数据一起判断；收齐了才取走。
- **为什么要转小写**：HTTP 请求头的名字不区分大小写，`Content-Length` 和 `content-length` 都是合法的。
- **`body` 是输出参数**：用普通引用传进来，函数把结果写进去交给调用者；返回值只负责说"成功了没有"。

### 第三站：handleHookEvent，这条消息是什么意思

```cpp
void Petwindow::handleHookEvent(const QByteArray& body)
{
	const QJsonObject json{ QJsonDocument::fromJson(body).object() };
	const QString eventName{ json.value("hook_event_name").toString() };
	const QString sessionId{ json.value("session_id").toString() };
	const QString toolName{ json.value("tool_name").toString() };
	const QString sessionTitle{ json.value("session_title").toString() };

	// ① 记下标题；调用工具时更新气泡（见下文）
	// ……

	// ② 更新这个会话的状态
	PetState newState{ PetState::Idle };
	if (eventName == "SessionEnd")
	{
		m_sessionStates.erase(sessionId);
		m_sessionTitles.erase(sessionId);
	}
	else if (stateHookEvent(eventName, toolName, newState))	// 认识这个事件才更新
	{
		m_sessionStates[sessionId] = newState;
		if (newState == PetState::Error)
		{
			scheduleErrorRecovery(sessionId);	// 只是登记：8 秒后才执行
		}
	}

	// ③ 合并所有会话，刷新宠物
	setState(combinedState());
}
```

`stateHookEvent` 负责把 Claude Code 的事件名"翻译"成宠物的 4 种状态：

| 事件 | 状态 |
|---|---|
| `PreToolUse` 且工具是 `AskUserQuestion`（Claude 要问你问题） | 等你回应 |
| `UserPromptSubmit`、`PreToolUse`、`PostToolUse` | 工作中 |
| `Stop` | 空闲 |
| `PermissionRequest` | 等你回应 |
| `StopFailure` | 出错 |

规则从上到下判断，**越特殊的规则越要写在前面**：如果"`PreToolUse` → 工作中"写在第一条，提问那条就永远轮不到了。

不认识的事件会让 `stateHookEvent` 返回 `false`，什么都不改。这一点很重要：端口是开着的，本机任何程序都能往里发东西，可能是别的事件、格式错误的 JSON，甚至根本不是 JSON。这时解析出来的全是空字符串，程序照样稳定。

---

## 同时开好几个会话怎么办

### 每个会话各存各的

最早的版本只有一个状态变量，问题很快就出现了：会话 A 正在工作，会话 B 发来 `Stop`，宠物就变成了空闲，可 A 明明还在干活。

解决办法是**按会话分开存**，需要显示的时候再合并：

```cpp
std::map<QString, PetState> m_sessionStates;	// 会话 ID → 状态
```

### 合并：挑出最紧急的那个

```cpp
static int urgency(PetState state)
{
	switch (state)
	{
	case PetState::Idle:		return 0;
	case PetState::Working:		return 1;
	case PetState::NeedsInput:	return 2;
	case PetState::Error:		return 3;
	}
	return 0;
}

PetState Petwindow::combinedState() const
{
	if (m_sessionStates.empty())
	{
		return PetState::Idle;	// 必须先判断：空 map 上 max_element 返回 end()，不能解引用
	}
	return std::max_element(m_sessionStates.begin(), m_sessionStates.end(),
		[](const auto& a, const auto& b)
		{
			return urgency(a.second) < urgency(b.second);
		})->second;
}
```

- **为什么单独写 `urgency()`**：`enum class` 不能直接当数字比较；就算能比，枚举的声明顺序也不代表紧急程度。单独写一个函数，优先级就是一个明确的决定。
- **为什么先判断空**：map 为空时，`max_element` 返回 `end()`，对它解引用是**未定义行为**。
- **为什么不能用 `[]` 查询**：`combinedState` 是常函数，而 `map` 的 `[]` 找不到键时会**插入**新元素，属于修改，编译不过。

### `[]` 和 `find` 的坑

上面提到了 `[]` 会插入元素。这是这个项目里我踩得最多的一个点，值得单独说。

`std::map` 的 `[]` 找不到键时，会插入一个新元素，值是默认值。这**不是**未定义行为，是标准规定好的，程序不会崩，只是结果不对，所以反而更难发现。

项目里有两处必须用 `find` 而不是 `[]`：

**1. 气泡查标题**

会话标题只在 `UserPromptSubmit`（你发消息时）才会带过来，而用到标题的是之后的 `PreToolUse`（Claude 调用工具时）。所以要先存起来，用的时候再查：

```cpp
if (!sessionTitle.isEmpty())
{
	m_sessionTitles[sessionId] = sessionTitle;	// 本来就要插入或更新，用 [] 正合适
}
if (eventName == "PreToolUse")
{
	auto it = m_sessionTitles.find(sessionId);	// 只查不插
	if (it != m_sessionTitles.end())
	{
		m_bubbleText = it->second + "：" + toolName;	// 例如"修复登录 bug：Read"
	}
	else
	{
		m_bubbleText = toolName;	// 不知道标题（比如宠物是中途才启动的）
	}
}
```

如果这里用 `[]`，宠物中途启动时，气泡会显示成"：Read"，map 里还会多一条空标题的记录。

**2. 出错恢复时查会话**（见下一节）

---

## 出错后的 8 秒自动恢复

会话出错（`StopFailure`）之后，Claude Code 不会再发后续事件，`Stop` 不会来。不处理的话，宠物会一直显示出错。所以出错时安排一个定时器，过 8 秒把这个会话恢复为空闲：

```cpp
void Petwindow::scheduleErrorRecovery(const QString& sessionId)
{
	QTimer::singleShot(kErrorRecoverMs, this, [this, sessionId]()
		{
			auto it = m_sessionStates.find(sessionId);
			if (it != m_sessionStates.end() && it->second == PetState::Error)
			{
				m_sessionStates[sessionId] = PetState::Idle;
			}
			setState(combinedState());
		});
}
```

这又是一次"登记"：`singleShot` 立刻返回，lambda 要 8 秒后才由事件循环执行。所以出错时，宠物**马上**显示出错，靠的是 `handleHookEvent` 最后那句 `setState(combinedState())`，而不是这里的 `setState`。

8 秒里可能发生很多事，`if` 里的两个条件正好防住两种变化：

- **`it != end()`：会话已经结束了。** 如果用 `[]`，会把已经 `erase` 掉的会话重新插回来。它的 `SessionEnd` 已经来过了，再也没人会删它，成了一个永远赶不走的"幽灵会话"。
- **`it->second == Error`：会话又开始干活了。** 不检查的话，会把正在工作的会话误改成空闲。

还有一个 C++ 细节：**`sessionId` 必须按值捕获**。8 秒后 lambda 执行时，`handleHookEvent` 早就返回了，它的局部变量 `sessionId` 已经销毁。按引用捕获会变成悬空引用；按值捕获则把字符串复制一份存进 lambda。注意，参数虽然是引用（`const QString&`），按值捕获复制的是引用**所指向的对象**。

---

## 改状态和画画面，是两件事

### setState：改状态的唯一入口

```cpp
void Petwindow::setState(PetState state)
{
	m_state = state;
	if (m_state == PetState::Idle)
	{
		m_bubbleText.clear();	// 所有会话都空闲了：清空气泡
	}
	update();	// 预约重画
}
```

不管是 hook 事件、8 秒恢复，还是按数字键测试，所有改状态的地方都经过 `setState`。"状态变了就必须一起做的事"（清空气泡、请求重画）放在这里，就不会被哪个地方漏掉。

### update()：只是"预约"

`update()` 并不会马上重画，它只是告诉 Qt"稍后需要重画"。真正的 `paintEvent` 要等当前的代码全部执行完、回到事件循环之后才发生。

拿第一幕的请求来说，完整的顺序是：

1. `handleHookEvent` → `setState` → `update()`：**预约**重画
2. 回到 `readyRead` 的 lambda：回复 200，断开连接
3. lambda 执行完，回到事件循环
4. Qt 调用 `paintEvent`：宠物这时才真正换成"工作中"

这也解释了为什么 `handleHookEvent` 里改完气泡文字不用单独 `update()`：后面的 `setState` 会预约，而真正画的时候，读到的已经是最终的文字。

### paintEvent：只读，不改

`paintEvent` 是整个程序的终点，它根据这几样东西决定画什么：

- `m_state`：画哪张表情
- `m_dragging`：是否正在被拖动（拖动时画"被拎起来"的图，不浮动，不画气泡）
- `m_bubbleText`：气泡里写什么（空字符串就不画）
- 闹钟是否在响：要不要浮动
- 秒表的时间：浮动到了哪个位置

它**只读不改**。这些东西都由别的函数修改，改完负责请求重画：

| `paintEvent` 读的东西 | 谁改它 | 改完怎么请求重画 |
|---|---|---|
| `m_state` | `setState`（来自 hook 事件、8 秒恢复、数字键） | `setState` 里 `update()` |
| `m_bubbleText` | `handleHookEvent` 设置，`setState` 清空 | 最后都经过 `setState` |
| `m_dragging` | 鼠标移动时设为 true，松开时设为 false | 各自 `update()` |
| 闹钟是否在响 | `enterEvent` 启动，`leaveEvent` / `hideEvent` 停止 | 闹钟响时自动 `update()` |
| 秒表的时间 | `enterEvent` 归零，之后自己在走 | 靠闹钟每 33 毫秒请求一次 |

一句话总结：**谁改了画面要用的东西，谁就负责调用 `update()`；`paintEvent` 只管按当前的值画。**

---

## 第二幕：和宠物互动

### 悬停时浮动：一个"闹钟"，一个"秒表"

鼠标移到宠物身上，宠物开始上下浮动。实现它用了两个名字很像、作用完全不同的工具：

- **闹钟** `QTimer m_animTimer`：每 33 毫秒响一次，每次请求重画。负责"**什么时候**画"
- **秒表** `QElapsedTimer m_animClock`：记录开始浮动后过了多久。负责"**画在哪**"

```cpp
void Petwindow::enterEvent(QEnterEvent*)
{
	m_animClock.restart();	// 秒表归零：sin(0) = 0，从中间开始，不会一上来就跳
	m_animTimer.start();	// 闹钟开始响
}

void Petwindow::leaveEvent(QEvent*)
{
	m_animTimer.stop();
	update();	// 最后重画一次：闹钟停了，位移为 0，回到中间
}
```

`paintEvent` 里按秒表算位移：

```cpp
qreal offset{};
if (m_animTimer.isActive())
{
	const qreal timeElapsed{ m_animClock.elapsed() / 1000.0 };	// 必须除以 1000.0，整数除法会丢掉小数
	offset = kFloatAmplitude * std::sin(2 * kPi * timeElapsed / kFloatPeriodSec);
}
painter.drawPixmap(QPointF(0, kFloatAmplitude + offset + kBubbleHeight), *pixmap);
```

几个设计点：

- **为什么需要闹钟**：浮动时没有任何变量被修改，变化的是"时间"本身。Qt 不知道时间会影响画面，所以只能由定时器不停地请求重画。
- **为什么按"经过的时间"算，而不是"闹钟响了几次"**：电脑卡顿时定时器会迟到，按次数算，动画就会变慢；按实际时间算，动画速度始终一致。
- **为什么用 `QPointF`**：小数坐标可以画在"半个像素"的位置，浮动才平滑；`QPoint` 会取整，一格一格地跳。
- **秒表为什么启动时就要 `start()`**：对没启动过的 `QElapsedTimer` 调用 `elapsed()` 是未定义行为。

### 拖动还是单击？

拖动和单击都是"按下、再松开"，程序怎么区分？

```cpp
void Petwindow::mouseMoveEvent(QMouseEvent* event)
{
	if (event->buttons() & Qt::LeftButton)
	{
		// 离按下的位置足够远（系统的拖动阈值），才算开始拖动
		if (!m_dragging && (m_pressGlobalPos - event->globalPosition().toPoint()).manhattanLength()
			>= QApplication::startDragDistance())
		{
			m_dragging = true;
			update();
		}
		move(event->globalPosition().toPoint() - m_dragOffset);
	}
}

void Petwindow::mouseReleaseEvent(QMouseEvent* event)
{
	if (event->button() == Qt::LeftButton)
	{
		if (m_dragging)		// 拖动结束
		{
			m_dragging = false;
			update();
			QSettings settings{ kSettingsName, kSettingsName };
			settings.setValue("pos", pos());	// 保存位置，下次启动时恢复
		}
		else				// 单击
		{
			activateClaudeWindow();
		}
	}
}
```

- **按下时不换图**：这时还不知道是单击还是拖动，等鼠标真的移动了一段距离再说。
- **"是不是拖动"只在一个地方判断**：`mouseMoveEvent` 判断并记进 `m_dragging`，`mouseReleaseEvent` 直接看结果。以后要调整规则，只改一处。
- **用屏幕坐标算距离**：拖动时窗口本身在移动，窗口坐标的参照点会跟着变。
- **`mouseMoveEvent` 不直接换图**：它只把 `m_dragging` 设为 true 并预约重画，换图是 `paintEvent` 的事。又是"改状态"和"画画面"分开。
- **松开时才保存位置**：拖一次只写一次注册表；如果在移动时保存，拖一下就要写几十上百次。单纯单击也不会写。

### 单击：把 Claude 切到前面

单击宠物会把 Claude 桌面应用的窗口切到最前面。这里用的是 Windows API，大部分是固定写法，只有两点值得一说：

```cpp
HWND hwnd{ FindWindowW(L"Chrome_WidgetWin_1", L"Claude") };
```

- **按"类名 + 标题"找窗口**：Claude 桌面应用是用 Electron 做的，窗口类名和 Chrome 浏览器一样，都是 `Chrome_WidgetWin_1`。只按类名找，可能找到的是 Chrome 或者 VS Code，所以要再加上标题。（这也意味着，如果哪天 Claude 改了窗口标题，这个功能就会失效。）
- **为什么只在单击时切换**：Windows 不允许程序随便把别的窗口切到前台（防止弹窗抢焦点）。刚被点击时，ClauPet 自己在前台，这时才被允许把 Claude 切过去。

---

## 第三幕：结束

### 会话结束

收到 `SessionEnd`，就把这个会话从两个 map 里都删掉。全部会话都结束后，map 为空，`combinedState` 返回空闲，`setState` 顺带清空气泡。

### 隐藏

右键菜单可以隐藏宠物。隐藏之后程序照样接收事件，只是看不见。`hideEvent` 里会停掉闹钟：

```cpp
void Petwindow::hideEvent(QHideEvent*)
{
	m_animTimer.stop();
}
```

为什么不靠 `leaveEvent`？点「隐藏」时鼠标在菜单上，窗口直接消失了，`leaveEvent` **不一定会来**。`hideEvent` 是一道保险：不管怎么隐藏的，闹钟都会停，不会在看不见的时候白白重画。

### 退出，以及一个析构顺序的细节

点击托盘菜单的「退出」：

1. 执行 `QApplication::quit()`，通知事件循环结束
2. `app.exec()` 返回
3. `main` 返回，局部变量按 C++ 的规则析构：后构造的先析构，先 `pet`，后 `app`

析构是 **C++ 的作用域规则**做的，不是 Qt。

`pet` 析构时，它的成员也会按**声明的相反顺序**析构。这里有一个容易忽略的细节：

```cpp
// 注意顺序：成员按声明顺序构造、按相反顺序析构。
// m_tray 记着 m_menu 的地址，所以 m_menu 必须先声明（后析构）
QMenu m_menu;
QSystemTrayIcon m_tray;
```

托盘图标通过 `setContextMenu(&m_menu)` 记住了菜单的地址。`m_menu` 声明在前，所以后析构；托盘析构的时候，菜单还在。如果顺序反过来，托盘析构时手里拿的就是一个悬空指针。

---

## 已知的限制

写这篇文章的过程中，我也发现了几个现在的设计没有处理好的地方：

- **收不到 `SessionEnd`**：如果 Claude Code 在会话还在工作时被强制关掉（崩溃、杀进程），`SessionEnd` 就没机会发出来。这个会话会一直留在 map 里，宠物一直显示工作中，只能重启 ClauPet。
- **气泡显示的不一定是"当前状态"所属的会话**：气泡显示的是"最近一次调用工具的是谁"。比如 A 在读文件、B 在等授权，宠物显示 B 的"等你回应"，气泡却是 A 的"修复登录 bug：Read"。
- **依赖 Claude 的窗口标题**：单击切换窗口靠的是标题 `Claude`，标题变了就会失效。

---

## 写在最后

回头看，这个项目里真正重要的设计其实就几条：

1. **登记和执行是两回事**：构造函数只登记，事件循环负责执行。`connect`、`singleShot`、`update()` 都是"现在登记，以后发生"。
2. **改状态和画画面分开**：谁改了状态谁就 `update()`，`paintEvent` 只读不改。
3. **改状态只有一个入口**：所有状态变化都经过 `setState`，该一起做的事不会漏。
4. **按会话分开存，显示时再合并**：多个会话互不覆盖。
5. **`map` 只想查询时用 `find`**：`[]` 会悄悄插入元素，造成"幽灵会话"。

这些东西单独看都不难，但只有把一条消息从头到尾走一遍，才能看清它们是怎么配合起来的。
