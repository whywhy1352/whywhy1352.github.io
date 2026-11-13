---
title: 线代前三章作业重做
tags:
  - 线性代数
categories:
  - 学习笔记
---

线性代数前三章的笔记和教材习题重做，按康奈尔笔记排版：左边是线索栏，右边是笔记栏。

<!-- more -->

## 第一章笔记（11.4）

### 1-1

<div class="cornell">
<div class="cue"></div>
<div class="note">

**一、特殊矩阵**

① $O$ 零矩阵，全为 0
② $E$ 单位矩阵，主对角线为 1，其余全是 0
③ 对角矩阵：非对角线上元素全为 0

</div>
<div class="cue"></div>
<div class="note">

**二、矩阵乘法**

① $A(BC)=(AB)C$，多用于多项相乘简化运算
② $A(B+C)=AB+AC$，顺序不能变
③ $AB=0 \nRightarrow A=0$ or $B=0$　一般
④ $AB=AC \nRightarrow B=C$　一般

</div>
<div class="cue"></div>
<div class="note">

**三、转置**

① $(A^T)^T=A$
② $(A+B)^T=A^T+B^T$
③ $(kA)^T=kA^T$
④ $(AB)^T=B^TA^T$，很像逆的性质：$(AB)^{-1}=B^{-1}A^{-1}$

</div>
<div class="cue">

GPT 给出了一个结论：A 是任何矩阵，

$$A=\frac{A+A^T}{2}+\frac{A-A^T}{2}$$

前为对称，后为反称。

</div>
<div class="note">

**四、对称与反称矩阵**

① 若 $A^T=A$，$A$ 为对称矩阵
② 若 $A^T=-A$，$A$ 为反〃；转置时对角线元素不变 $\Rightarrow a_{ii}=0$

</div>
</div>

### 1-2

<div class="cornell">
<div class="cue">

这是矩阵乘法的一种理解 ⇒

</div>
<div class="note">

**一、线性组合**

向量即 $\begin{bmatrix}x_1\\x_2\\x_3\end{bmatrix}$，或 $[x_1,x_2,x_3]^T$。我们将 $k_1\alpha_1+k_2\alpha_2+\cdots+k_m\alpha_m$ 称为向量线性组合。

$A=[\alpha_1,\alpha_2,\alpha_3]$【?1】，对 $A\begin{bmatrix}x_1\\x_2\\x_3\end{bmatrix}=x_1\alpha_1+x_2\alpha_2+x_3\alpha_3$，即为 $\alpha_1,\alpha_2,\alpha_3$ 作线性组合。

</div>
<div class="cue"></div>
<div class="note">

**二、分块矩阵**

**1. 原则**

A 若分成 $\begin{bmatrix}A_{11}&A_{12}\\A_{21}&A_{22}\end{bmatrix}$，分块不需要大小相等，也不要求每块都是矩阵。

</div>
<div class="cue"></div>
<div class="note">

**2. 分块乘法**

$$\begin{bmatrix}A&B\\C&D\end{bmatrix}\begin{bmatrix}P&Q\\A&S\end{bmatrix}=\begin{bmatrix}AP+BR&AQ+BS\\CP+DQ&CQ+DS\end{bmatrix}$$

千万注意内部顺序。【?2】

</div>
<div class="cue">

剧透：分块矩阵求逆：

$$\begin{bmatrix}A&B\\O&C\end{bmatrix}\begin{bmatrix}X&Y\\P&Z\end{bmatrix}=\begin{bmatrix}E&O\\O&E\end{bmatrix}$$

$$
\begin{aligned}
&\begin{bmatrix}AX+BP&AY+BZ\\CP&CZ\end{bmatrix}\\
=&\begin{bmatrix}E&O\\O&E\end{bmatrix}
\end{aligned}
$$

比较系数。

$$\begin{cases}AX+BP=E\\AY+BZ=0\\CP=0\\CZ=E\end{cases}$$

已知 $C$ 非零 $\Rightarrow P=0$。

</div>
<div class="note">

**3. 分块转置**

$$\begin{bmatrix}A&B\\C&D\end{bmatrix}^T=\begin{bmatrix}A^T&C^T\\B^T&D^T\end{bmatrix}$$

有必要与分块矩阵求逆区分

</div>
</div>

### 1-3

<div class="cornell">
<div class="cue"></div>
<div class="note">

**1. 定义**：对 $E$ 进行一次初等行变换得到的矩阵叫初等矩阵。

</div>
<div class="cue"></div>
<div class="note">

**2. 类型**

① 两行交换：$\begin{bmatrix}0&1&0\\1&0&0\\0&0&1\end{bmatrix}$ ⇒ 交换，记为 $E_{ij}$

② 某行倍乘：$\begin{bmatrix}1&0&0\\0&5&0\\0&0&1\end{bmatrix}$ ⇒ 乘一个数，记为 $E_i(k)$

③ 某两行倍加：$\begin{bmatrix}1&0&0\\0&1&0\\-2&0&1\end{bmatrix}$ ⇒ 倍加，记为 $E_{ij}(k)$，$j\times k$ 加到 $i$ 上

</div>
<div class="cue"></div>
<div class="note">

**3. 左乘 | 右乘**

左乘 ⇒ 行，执行一次行变换
右乘 ⇒ 列，〃　列　〃

</div>
<div class="cue"></div>
<div class="note">

**4. 初等矩阵可逆**

本质：初等变换可被撤销

</div>
<div class="cue"></div>
<div class="note">

**5. 矩阵等价**

如果 A(B)【?3】可以通过有限次初等变换得到 B，称 A、B 等价，记作 $B=PAQ$

</div>
<div class="cue">

| 行阶梯形 | 等价标准形 |
| --- | --- |
| 只能行变换【?4】 | 行、列变换都可 |
| 能确定秩 | 能确定秩 |

</div>
<div class="note">

**6. 等价标准形**

一个除左上角的单位阵以外都是 0 的矩阵形式叫等价标准形 $\begin{bmatrix}E&O\\O&O\end{bmatrix}$

可以使用行、列变换

</div>
</div>

## 第一章 教材重做

### 1-1 习

<div class="cornell">
<div class="cue"></div>
<div class="note">

**4(4)**

$$\begin{bmatrix}2&1&3\\1&1&0\\1&0&0\end{bmatrix}\begin{bmatrix}2&1&1\\1&1&0\\3&0&0\end{bmatrix}=\begin{bmatrix}14&3&2\\3&2&1\\2&1&1\end{bmatrix}$$

</div>
<div class="cue"></div>
<div class="note">

**13**

$(A^TA)^T=A^T(A^T)^T=A^TA$　　$(AA^T)^T=(A^T)^TA^T=AA^T$

</div>
<div class="cue"></div>
<div class="note">

**14(1)**

$(P^TAP)^T=P^TA^TP$　　$A^T=A$　　$P^TA^TP=P^TAP$

**14(2)**

同 (1) 理

</div>
</div>

### 1-2 习

<div class="cornell">
<div class="cue"></div>
<div class="note">

**1.**

$a^T=[1,2,-1]$　　$a^Ta=[1,2,-1]\begin{bmatrix}1\\2\\-1\end{bmatrix}=6$

$$aa^T=\begin{bmatrix}1\\2\\-1\end{bmatrix}[1,2,-1]=\begin{bmatrix}1&2&-1\\2&4&-2\\-1&-2&1\end{bmatrix}$$

$$
\begin{aligned}
(aa^T)^{100}&=aa^Taa^T\cdots aa^T=a(a^Ta)^{99}a^T\\
&=6^{99}\begin{bmatrix}1&2&-1\\2&4&-2\\-1&-2&1\end{bmatrix}
\end{aligned}
$$

（旁边草稿：$\frac{200-2}{2}$、198）

</div>
<div class="cue">

拓展：对于 $a=\begin{bmatrix}1\\2\\3\end{bmatrix}$，$b=\begin{bmatrix}4\\5\\6\end{bmatrix}$

$$b^Ta=[4,5,6]\begin{bmatrix}1\\2\\3\end{bmatrix}=32$$

$$
\begin{aligned}
ab^T&=\begin{bmatrix}1\\2\\3\end{bmatrix}[4,5,6]\\
&=\begin{bmatrix}4&5&6\\8&10&12\\12&15&18\end{bmatrix}
\end{aligned}
$$

注意主对角线　4+10+18=32

$(ab^T)_{ij}=a_ib_j$，当 $i=j$：$a_1b_1,\ a_2b_2,\ \cdots$

Σ 之后得到 $b^Ta$，$\operatorname{tr}(ab^T)=b^Ta$

我们把主对角线上的元素称为 trace（迹）

</div>
<div class="note">

**3.**（题号被红笔圈出）

$$(ab^T)^T=ba^T=\begin{bmatrix}1&1&1\\2&2&2\\3&3&3\end{bmatrix}$$

$\textcolor{red}{b^Ta=6}$

</div>
<div class="cue"></div>
<div class="note">

**4.**

$(E-aa^T)(E+3aa^T)$
$=E+3aa^T-aa^T-3aa^Taa^T$
$=E+2aa^T-3k\,aa^T=E$
$(2-3k)aa^T=0$，$k=\frac{2}{3}$
$a^Taa^Ta\neq0$，$aa^T\neq0$【?5】

</div>
<div class="cue"></div>
<div class="note">

**5.**

$$AA^T=[a_1,a_2,\cdots,a_n]\begin{bmatrix}a_1^{\textcolor{red}{T}}\\a_2^{\textcolor{red}{T}}\\\vdots\\a_n^{\textcolor{red}{T}}\end{bmatrix}=\sum_{k=1}^n\textcolor{red}{\xcancel{a_k^2}}\quad\textcolor{red}{\sum_{k=1}^n a_ka_k^T}$$

$$A^TA=\begin{bmatrix}a_1^{\textcolor{red}{T}}\\\vdots\\a_n^{\textcolor{red}{T}}\end{bmatrix}[a_1\cdots a_n]=\begin{bmatrix}\textcolor{red}{\xcancel{a_1^2}\ a_1^Ta_1}&a_1^{\textcolor{red}{T}}a_2&\cdots&a_1^{\textcolor{red}{T}}a_n\\a_2a_1&\textcolor{red}{\xcancel{a_2^2}\ a_2^Ta_2}&\cdots&a_2a_n\\\vdots&&&\end{bmatrix}$$

（打叉的部分原来是黑笔，被红笔划掉）

</div>
</div>

### 1-3 习

<div class="cornell">
<div class="cue"></div>
<div class="note">

**2.**

$$PA=\begin{bmatrix}0&1&0\\1&0&0\\0&0&1\end{bmatrix}\begin{bmatrix}1&2&3\\4&5&6\\7&8&9\end{bmatrix}=\begin{bmatrix}4&5&6\\1&2&3\\7&8&9\end{bmatrix}\quad\text{交换 1、2 行}$$

$$AP=\begin{bmatrix}1&2&3\\4&5&6\\7&8&9\end{bmatrix}\begin{bmatrix}0&1&0\\1&0&0\\0&0&1\end{bmatrix}=\begin{bmatrix}2&1&3\\5&4&6\\8&7&9\end{bmatrix}\quad\text{交换 1、2 列}$$

$$QA=\begin{bmatrix}1&3&0\\0&1&0\\0&0&1\end{bmatrix}\begin{bmatrix}1&2&3\\4&5&6\\7&8&9\end{bmatrix}=\begin{bmatrix}13&17&21\\4&5&6\\7&8&9\end{bmatrix}\quad\text{2 行}\times3+\text{1 行上}$$

$$AQ=\begin{bmatrix}1&2&3\\4&5&6\\7&8&9\end{bmatrix}\begin{bmatrix}1&3&0\\0&1&0\\0&0&1\end{bmatrix}=\begin{bmatrix}1&5&3\\4&17&6\\7&29&9\end{bmatrix}\quad\text{1 列}\times3+\text{2 列上}$$

</div>
<div class="cue">

$$E=\begin{bmatrix}1&0&0\\0&1&0\\0&0&1\end{bmatrix}$$

</div>
<div class="note">

**3.**

$E_{32}(-2)=\begin{bmatrix}1&0&0\\0&1&0\\0&-2&1\end{bmatrix}$，$E_{12}=\begin{bmatrix}0&1&0\\1&0&0\\0&0&1\end{bmatrix}$

$$\text{原式}=\begin{bmatrix}1&2&3\\2&3&4\\-1&-2&-3\end{bmatrix}E(1,2)=\begin{bmatrix}2&1&3\\3&2&4\\-2&-1&-3\end{bmatrix}$$

</div>
<div class="cue">

补充：
r = row 行
c = column 列

</div>
<div class="note">

**4.**

$A_1=E_{32}(2)A$　　$A_2=E_{32}(2)AE_{21}(-1)$　　$A_3=E_3(3)E_{32}(2)AE_{21}(-1)$

$A_4=E_3(3)E_{32}(2)AE_{21}(-1)E_{13}$

$\textcolor{red}{E_2(3)E_{31}(2)AE_{21}(-1)E_{31}}$【?6】

</div>
<div class="cue"></div>
<div class="note">

**5(1)**

$$A=\begin{bmatrix}1&1&-1\\0&2&-1\\0&2&1\end{bmatrix}=\begin{bmatrix}1&1&-1\\0&2&-1\\0&0&2\end{bmatrix}$$

**5(3)**

$$A=\begin{bmatrix}1&1&-1\\0&2&-1\\0&0&2\end{bmatrix}=\begin{bmatrix}1&1&0\\0&2&0\\0&0&2\end{bmatrix}=\begin{bmatrix}1&0&0\\0&1&0\\0&0&1\end{bmatrix}$$

</div>
</div>

### 1-3 提

<div class="cornell">
<div class="cue"></div>
<div class="note">

**1.** 第三个 (3)

</div>
<div class="cue"></div>
<div class="note">

**3.**

$P_1=\begin{bmatrix}1&0&0\\0&1&0\\0&0&1\end{bmatrix}$　$P_2=\begin{bmatrix}1&0&0\\\textcolor{red}{2}&1&0\\0&0&1\end{bmatrix}$　$P_3=\begin{bmatrix}1&0&0\\\textcolor{red}{2}&1&0\\-1&0&1\end{bmatrix}$　$P_4=\begin{bmatrix}1&0&0\\\textcolor{red}{6}&3&0\\-1&0&1\end{bmatrix}$　$P_5\begin{bmatrix}-1&0&1\\\textcolor{red}{6}&3&0\\1&0&0\end{bmatrix}$

（红色数字是红笔写的，两个 6 覆盖在原来的黑色数字上）

</div>
</div>

## 第二章笔记（11.5）

### 2-1

<div class="cornell">
<div class="cue"></div>
<div class="note">

**1. 行列式**

只有方阵有行列式

对于二阶方阵 $\begin{vmatrix}a&b\\c&d\end{vmatrix}=ad-bc$

</div>
<div class="cue"></div>
<div class="note">

**2. 余子式**

对于元素 $a_{ij}$，删除 $i$ 行 $j$ 列的原行列式组成了一个新的行列式，计算出的值叫这个元素的余子式，记为 $M_{ij}$

</div>
<div class="cue">

我的混淆：以为 $A_{ij}$ 是 $(-1)^{i+j}\textcolor{red}{a_{ij}}M_{ij}$

</div>
<div class="note">

**3. 代数余子式**

$$A_{ij}=(-1)^{i+j}M_{ij}$$

</div>
</div>

### 2.2 行列式的性质

<div class="cornell">
<div class="cue"></div>
<div class="note">

**1. 转置性质**

$|A^T|=|A|$

证：显然，行列式都可写成三角形，转置不影响对角线

作用：由于我们只在意行列式的值，因此行的性质对列也成立

</div>
<div class="cue"></div>
<div class="note">

**2. 交换性质**

$D'=-D$，交换两行（列）行列式变号

证：按行展开计算可证。

</div>
<div class="cue">

后面一节计算中还有用 ⇒

</div>
<div class="note">

推论：若两行（列）相同，$D'=-D=0$

</div>
<div class="cue"></div>
<div class="note">

**3. 提取性质**

$D'=kD$，对于一行（列）

证：计算可证

推论：若两行（列）成比例，则 $D=0$。

</div>
<div class="cue">

伴随有用到 ⇒

</div>
<div class="note">

注意：若 $n$ 行（列）都有公因子 $k$，那么 $D=k^nD'$

</div>
<div class="cue">

一般 $|A+B|=|A|+|B|$ ✗

</div>
<div class="note">

**4. 拆分性质**

线性计算：

$$\begin{vmatrix}a+b&c+d\\e&f\end{vmatrix}=\begin{vmatrix}a&c\\e&f\end{vmatrix}+\begin{vmatrix}b&d\\e&f\end{vmatrix}$$

合法。

</div>
<div class="cue">

算是 4 引理 →

</div>
<div class="note">

**5. 倍加性质**

$D\xrightarrow{R_i\leftarrow R_i+R_i\cdot k}D'$【?7】，$D=D'$

证：$D'=D+D''$，$D''$ 有两行成比例，于是 $D''=0$。

</div>
</div>

### 2.3 行列式计算

<div class="cornell">
<div class="cue">

一般选好算的。

</div>
<div class="note">

**1. 按某一行（列）展开**

$$D=\sum_{j=1}^n a_{ij}A_{ij}$$

</div>
<div class="cue">

相当于第 k 行（列）换成 i 行（列）再按 k 展开，计算行列式的值。

</div>
<div class="note">

引申：$\sum_{j=1}^n a_{ij}A_{kj}=0\quad(i\neq k)$

</div>
<div class="cue"></div>
<div class="note">

**2. 化为三角形行列式**

$$D=\prod_{i=1}^n a_{ii}$$

注意：交换行/列，须变号

</div>
<div class="cue">

```cpp
int sum{};
for (int i{1}; i <= n; ++i)
{
    for (int j{i+1}; j < n; ++j)
    {
        sum *= (Xj - Xi);
    }
}
```

</div>
<div class="note">

**3. 范德蒙德行列式**

$$D_n=\begin{vmatrix}1&1&\cdots&1\\x_1&x_2&\cdots&x_n\\x_1^2&x_2^2&\cdots&x_n^2\\\vdots&\vdots&&\vdots\\x_1^{n-1}&x_2^{n-1}&\cdots&x_n^{n-1}\end{vmatrix}$$

$$D_n=\prod_{1\le i<j\le n}(x_j-x_i)$$

例：$(x_2-x_1)(x_3-x_2)(x_3-x_2)$【?8】

</div>
<div class="cue"></div>
<div class="note">

**4. 各行（列）元素和相等的行列式**

例：$D=\begin{vmatrix}2&1&0\\0&2&1\\1&0&2\end{vmatrix}$ ⇒ 观察到每一行元素之和相同

$$
\begin{aligned}
D&=\begin{vmatrix}3&1&0\\3&2&1\\3&0&2\end{vmatrix}=3\begin{vmatrix}1&1&0\\1&2&1\\1&0&2\end{vmatrix}\\
&=3\begin{vmatrix}1&1&0\\0&1&1\\0&-1&2\end{vmatrix}=3\begin{vmatrix}1&1&0\\0&1&1\\0&0&3\end{vmatrix}=9
\end{aligned}
$$

</div>
<div class="cue"></div>
<div class="note">

**5. 箭形行列式**

$$
\begin{aligned}
&\begin{vmatrix}x&b_1&b_2&\cdots&b_n\\c_1&a_1&0&\cdots&0\\c_2&0&a_2&\cdots&0\\\vdots&\vdots&\vdots&\ddots&\vdots\\c_n&0&0&\cdots&a_n\end{vmatrix}\\
\xrightarrow[\text{第一列}]{\text{除第一列的所有列 }-\frac{c_i}{a_i}\text{ 倍加到}}\ &\begin{vmatrix}x-\sum\frac{b_ic_i}{a_i}&b_1&b_2&\cdots&b_n\\0&a_1&0&\cdots&0\\0&0&a_2&\cdots&0\\\vdots&\vdots&\vdots&\ddots&\vdots\\0&0&0&\cdots&a_n\end{vmatrix}\\
=\ &\prod_{i=1}^n a_i\left(x-\sum\frac{b_ic_i}{a_i}\right)
\end{aligned}
$$

</div>
</div>

### 2.4 分块行列式

<div class="cornell">
<div class="cue"></div>
<div class="note">

**1. 三角形**

$\begin{vmatrix}A&O\\C&B\end{vmatrix}=|A||B|$

</div>
<div class="cue">

<span class="red">是 mn 非 m+n，因为要保证顺序</span>

</div>
<div class="note">

$\begin{vmatrix}O&A\\B&O\end{vmatrix}=(-1)^{mn}|A||B|$

</div>
<div class="cue"></div>
<div class="note">

$|AB|=|A||B|$
$|A^k|=|A|^k$

</div>
</div>

## 第二章 教材重做

### 2-1 习

<div class="cornell">
<div class="cue"></div>
<div class="note">

**1.** $A_{32}=(-1)^5(-6)=6$

</div>
</div>

### 2-2 习

<div class="cornell">
<div class="cue"></div>
<div class="note">

**1.**

$$
\begin{aligned}
\det(A+B)&=[3a_1,\ a_1+a_2,\ -3a_3]=(-9)[a_1,\ a_1+a_2,\ a_3]\\
&=(-9)(\det(A)+0)=-18
\end{aligned}
$$

</div>
<div class="cue"></div>
<div class="note">

**3.**

$|a_3\ \ a_2\ \ a_1\ \ b_1|=-m$　　$|a_3,\ a_2,\ a_1,\ b_2|=n$

$|a_3,\ a_2,\ a_1,\ b_1+b_2|=|a_3\ \ a_2\ \ a_1\ \ b_1|+|a_3\ \ a_2\ \ a_1\ \ b_2|=n-m$

</div>
<div class="cue"></div>
<div class="note">

**4(3)**

$|A^T|=|A|=|-A|=(-1)^n|A|$，$n$ 为奇数，$|A|=0$

</div>
<div class="cue"></div>
<div class="note">

**5.**

原式 $=\prod_{i=1}^n b_i^2\,|A|=\prod_{i=1}^n b_i^2\,c$

</div>
<div class="cue"></div>
<div class="note">

**6.(1)**

$$
\begin{aligned}
\text{原式}&=adf\begin{vmatrix}-b&c&e\\b&-c&e\\b&c&-e\end{vmatrix}=abcdef\begin{vmatrix}-1&1&1\\1&-1&1\\1&1&-1\end{vmatrix}\\
&=abcdef\begin{vmatrix}-1&1&1\\0&0&2\\1&1&-1\end{vmatrix}=-abcdef\begin{vmatrix}-1&1&1\\1&1&-1\\0&0&2\end{vmatrix}\\
&=-abcdef\begin{vmatrix}-1&1&1\\0&2&0\\0&0&2\end{vmatrix}=4abcdef
\end{aligned}
$$

</div>
</div>

### 2-3 习

<div class="cornell">
<div class="cue"></div>
<div class="note">

**1(1)**

原式 $=(-1)^{\textcolor{red}{n}}\,n!$（指数 $n$ 被红笔圈出）

<span class="red">$\frac{n(n+1)}{2}$</span>【?9】
<span class="red">$(n-1)+(n-2)+\cdots+1=\frac{n(n-1)}{2}$</span>
<span class="red">最后一个交换到第一个要 $n-1$</span>
<span class="red">〃　〃　〃　〃　$n-2$。</span>

</div>
<div class="cue"></div>
<div class="note">

**1(3)**【?10】

原式 $=1\times$（对角线全是 1、下面一斜行是 $x_2, x_3, \cdots, x_n$ 的行列式）$+\ (-1)^{1+n}x_n\times$（对角线是 $x_1, x_2, \cdots, x_{n-1}$、上面一斜行是 1 的行列式）

$=1+(-1)^{1+n}x_n!$　$\textcolor{red}{=\prod_{i=1}^n x_i}$

（$(-1)^{1+n}$ 是用插入号补写在 $x_n$ 上方的）

</div>
<div class="cue"></div>
<div class="note">

**1(7)**

$$\text{原式}=(-1)\times\overset{-7}{\begin{vmatrix}5&4&2\\1&1&1\\2&3&-2\end{vmatrix}}+(-1)\times\overset{-13}{\begin{vmatrix}5&3&2\\1&2&1\\2&-1&-2\end{vmatrix}}=20$$

（两个行列式上方标的是它们的值）

</div>
<div class="cue"></div>
<div class="note">

**2(2)**【?11】

$$
\begin{aligned}
\text{原式}&=\begin{vmatrix}a_1&a_2&\cdots&a_n\\a_1&a_2&\cdots&a_n\\\vdots&\vdots&&\vdots\\a_1&a_2&\cdots&a_n\end{vmatrix}+\begin{vmatrix}1&a_2&\cdots&a_n\\a_1&1&\cdots&a_n\\\vdots&\vdots&\ddots&\vdots\\a_1&a_2&\cdots&1\end{vmatrix}\\
&=\begin{vmatrix}1-a_1&0&\cdots&a_n-1\\0&1-a_2&\cdots&a_n-1\\\vdots&\vdots&\ddots&\vdots\\a_1&a_2&\cdots&1\end{vmatrix}\\
&=\begin{vmatrix}1-a_1&0&\cdots&a_n-1\\0&1-a_2&\cdots&a_n-1\\\vdots&\vdots&\ddots&\vdots\\0&0&\cdots&1-\sum_{i=1}^n\frac{a_i(a_n-1)}{1-a_i}\end{vmatrix}\\
&=\prod_{i=1}^{n-1}(1-a_i)\left(1-\sum_{i=1}^n\frac{a_i(a_n-1)}{1-a_i}\right)
\end{aligned}
$$

<span class="red">红笔重做（写在页面下方）：</span>

$$
\color{red}
\begin{aligned}
&\begin{vmatrix}1&0&\cdots&-1\\0&1&\cdots&-1\\\vdots&\vdots&&\vdots\\a_1&a_2&\cdots&1+a_n\end{vmatrix}\\
=&\begin{vmatrix}1&0&\cdots&-1\\0&1&\cdots&-1\\\vdots&\vdots&&\vdots\\0&0&\cdots&1+a_n+\sum_{i}^{n-1}a_i\end{vmatrix}=1+\sum_{i=1}^n a_i
\end{aligned}
$$

</div>
<div class="cue"></div>
<div class="note">

**2(3)**

$$
\begin{aligned}
\text{原式}&=\begin{vmatrix}s+\sum_{i=1}^n i&s+\sum_{i=1}^n i&\cdots&s+\sum_{i=1}^n i\\2&2+s&\cdots&2\\3&3&\cdots&3\\\vdots&\vdots&&\vdots\\n&n&\cdots&n+s\end{vmatrix}\\
&=\begin{vmatrix}s+\sum_{i=1}^n i&0&\cdots&0\\2&s&\cdots&0\\3&0&\cdots&0\\\vdots&\vdots&&\vdots\\n&0&\cdots&s\end{vmatrix}=s^{n-1}\left(s+\sum_{i=1}^n i\right)
\end{aligned}
$$

</div>
<div class="cue"></div>
<div class="note">

**2(6)**

$$
\begin{aligned}
\text{原式}&=\begin{vmatrix}(n-1)b+a&b&\cdots&b&a\\(n-1)b+a&b&\cdots&a&b\\\vdots&\vdots&&\vdots&\vdots\\(n-1)b+a&a&\cdots&b&b\\(n-1)b+a&b&\cdots&b&b\end{vmatrix}\\
&=\begin{vmatrix}0&0&\cdots&0&a-b\\0&0&\cdots&a-b&0\\\vdots&\vdots&&\vdots&\vdots\\0&a-b&\cdots&0&0\\(n-1)b+a&b&\cdots&b&b\end{vmatrix}\\
&=(-1)^{\textcolor{red}{n}}\prod^{n-1}(a-b)\,[(n-1)b+a]
\end{aligned}
$$

<span class="red">指数 $n$ 被圈出，上方写了 $\frac{n(n-1)}{2}$</span>

</div>
<div class="cue"></div>
<div class="note">

**2(8)**【?12】

$$
\begin{aligned}
&\begin{vmatrix}a_1&0&0&\cdots&-a_n\\0&a_2&0&\cdots&-a_n\\\vdots&\vdots&\vdots&&\vdots\\1&1&1&\cdots&a_n+1\end{vmatrix}\\
=&\begin{vmatrix}a_1&0&0&\cdots&-a_n\\0&a_2&0&\cdots&-a_n\\\vdots&\vdots&\vdots&&\vdots\\0&0&0&\cdots&a_n+1+\sum_{i}^{n-1}\frac{a_n}{a_i}\end{vmatrix}\\
=&\prod_{i=1}^{n-1}a_i\left(a_n+1+\sum_{i}^{n-1}\frac{a_n}{a_i}\right)
\end{aligned}
$$

</div>
</div>

### 2-4 习

<div class="cornell">
<div class="cue"></div>
<div class="note">

**1(4)**

$$\text{原式}=-\begin{vmatrix}1&2&3&0&0\\0&1&4&0&0\\0&0&5&0&0\\2&2&1&2&2\\3&3&3&3&1\end{vmatrix}=-\begin{vmatrix}A&O\\B&C\end{vmatrix}=-|A||C|=(-5)\times(-4)=20$$

</div>
<div class="cue"></div>
<div class="note">

**2.**【?13】

$$\begin{vmatrix}O&B\\A&O\end{vmatrix}=(-1)^{mn}\,6=\begin{vmatrix}O&B\\A&C\end{vmatrix}=\begin{vmatrix}D&B\\A&O\end{vmatrix}$$

</div>
<div class="cue"></div>
<div class="note">

**3.**

$$\begin{vmatrix}A&B\\B&A\end{vmatrix}=\begin{vmatrix}A+B&A+B\\B&A\end{vmatrix}=\begin{vmatrix}A+B&O\\B&A-B\end{vmatrix}=|A+B|\cdot|A-B|$$

</div>
</div>

## 第三章笔记（11.6）

### 3.1 可逆矩阵

<div class="cornell">
<div class="cue"></div>
<div class="note">

**1. 定义**：为了使矩阵某程度上能作类似于代数除法的运算，我们引入逆矩阵。

对于 $n$ 阶方阵 $A$、$B$，若 $AB=BA=E$，则 $A$、$B$ 可逆，$A$ 与 $B$ 互为可逆矩阵。

$B=A^{-1}$，$A=B^{-1}$，于是 $AA^{-1}=A^{-1}A=E$

</div>
<div class="cue"></div>
<div class="note">

**2. 判定**：$|A|\neq0 \Leftrightarrow A$ 可逆

</div>
<div class="cue">

① 设 A B【?14】都是 A 的逆矩阵。
有 $AB=BA=AC=CA=E$
于是 $B=BE=BAC=EC=C$，$B=C$。

</div>
<div class="note">

**3. 可逆矩阵性质**

① 唯一
② $(A^{-1})^{-1}=A$

</div>
<div class="cue">

③ 对于 $AB$ 与 $B^{-1}A^{-1}$：
$AB\cdot B^{-1}A^{-1}=AEA^{-1}=E$。

</div>
<div class="note">

③ $(AB)^{-1}=B^{-1}A^{-1}$，有点像转置。
④ $(kA)^{-1}=\frac{1}{k}A^{-1}$，$k\neq0$。好像在伴随会用到？
⑤ $(A^m)^{-1}=(A^{-1})^m$

</div>
<div class="cue">

<span class="red">别忘了转置</span>

</div>
<div class="note">

**4. 伴随矩阵**

对于 $A=\begin{bmatrix}a_{11}&a_{12}\\a_{21}&a_{22}\end{bmatrix}$　　$A_{11}=a_{22}$，$A_{12}=-a_{21}$，$A_{21}=-a_{12}$，$A_{22}=a_{11}$

$$A^*=\begin{bmatrix}A_{11}&A_{12}\\A_{21}&A_{22}\end{bmatrix}^T=\begin{bmatrix}A_{11}&A_{21}\\A_{12}&A_{22}\end{bmatrix}=\begin{bmatrix}a_{22}&-a_{12}\\-a_{21}&a_{11}\end{bmatrix}$$

</div>
<div class="cue"></div>
<div class="note">

**5. 伴随矩阵性质**

① 核心：$AA^*=A^*A=|A|E$

</div>
<div class="cue">

$|A|\neq0$ 是使用 $A^*$ 求 $A^{-1}$ 的前提

</div>
<div class="note">

求逆：$A\left(\frac{A^*}{|A|}\right)=E$，$\frac{A^*}{|A|}=A^{-1}$　　$|A|\neq0$

</div>
</div>

### 3.2 n 元线性方程组

<div class="cornell">
<div class="cue"></div>
<div class="note">

**1. 线性方程的矩阵形式**

考虑一个 $n$ 元方程组：

$$
\begin{aligned}
a_{11}x_1+a_{12}x_2+\cdots+a_{1n}x_n&=b_1\\
a_{21}x_1+a_{22}x_2+\cdots+a_{2n}x_n&=b_2\\
&\ \,\vdots\\
a_{n1}x_1+a_{n2}x_2+\cdots+a_{nn}x_n&=b_n
\end{aligned}
$$

$$\Rightarrow\underbrace{\begin{bmatrix}a_{11}&a_{12}&\cdots&a_{1n}\\\vdots&&\ddots&\vdots\\a_{n1}&a_{n2}&\cdots&a_{nn}\end{bmatrix}}_{A}\underbrace{\begin{bmatrix}x_1\\\vdots\\x_n\end{bmatrix}}_{x}=\underbrace{\begin{bmatrix}b_1\\\vdots\\b_n\end{bmatrix}}_{b}\Rightarrow Ax=b$$

</div>
<div class="cue"></div>
<div class="note">

**2. 求解**

**① 用逆矩阵求解**

对 $Ax=b$，假设 $|A|\neq0$（可逆）

</div>
<div class="cue">

左乘！

</div>
<div class="note">

$A^{-1}Ax=A^{-1}b$　　$x=A^{-1}b$

</div>
<div class="cue"></div>
<div class="note">

且此方法的 $x$ 唯一。反证：设 $x_1, x_2$ 都是解，有 $Ax_1=b$，$Ax_2=b$。

$A(x_1-x_2)=0$　　$|A|\,|x_1-x_2|=0$　　$|A|\neq0$，则 $x_1-x_2=0$　　$x_1=x_2$

</div>
<div class="cue">

Gemini 给出了一个有意思的看法【?16】

$b=x_1a_1+x_2a_2+\cdots+x_ia_1+x_na_n$

i 列被换了：$b=x_1a_1+x_2a_2+\cdots+x_ib_1+x_na_n$

$$\begin{vmatrix}a_{11}&a_{12}&\cdots&b&\cdots&a_{1n}\\a_{21}&a_{22}&\cdots&b&\cdots&a_{2n}\\\vdots&\vdots&&&&\\a_{n1}&a_{n2}&\cdots&b&\cdots&a_{nn}\end{vmatrix}$$

</div>
<div class="note">

**② 克拉默法则**

对于 $Ax=b$，若 $|A|=D\neq0$，那么 $x_i=\frac{D_i}{D}$

$D_i$ 表示将系数矩阵 $A$ 的第 $i$ 列替换为 $b$ 后的新行列式

证明：$A^{-1}=\frac{A^*}{|A|}$　　$x=A^{-1}b$

由于进行了转置，$A^*$ 的第 $i$ 行为 $(A_{1i},A_{2i},\cdots,A_{ni})$，乘上列向量（$A^*b$）【?15】$=b_1A_{1i}+b_2A_{2i}+\cdots+b_nA_{ni}$

</div>
<div class="cue"></div>
<div class="note">

**③ 增广矩阵法（高斯消元）**

对于 $Ax=b$，将 $A$、$b$ 拼一起 $[A|b]$

我们要通过初等行变换 $[A|b]\Rightarrow[E|x]$

顺便，如果化不成 $E$，可以看出 $|A|=0$，$r(A)<n$，有无多解【?17】

</div>
</div>

## 待确认（发布前删掉这一节和正文里的【?n】）

1. 【?1】1-2：$A=[a_1,a_2,a_3]$ 的字母像拉丁字母 a，上下文都写 α，这里统一成了 α。
2. 【?2】分块乘法：第二个矩阵左下角看起来像 A，结果左下角写的是 CP+DQ，按原样抄了；按乘法规则应是 R 和 CP+DR。
3. 【?3】矩阵等价：「A(B)」里括号中的 B 是什么意思？
4. 【?4】旁注「只能行变换」：第一个字很潦草，按意思读成「只」。
5. 【?5】1-2 习第 4 题最后一行「aᵀa aᵀa ≠ 0」看不出想表达什么；k 在笔记里没有定义，猜是 k = aᵀa。
6. 【?6】1-3 习第 4 题的红笔：原来写在 5(1) 那一行右边，理解为 A₄ 的订正；第一个 E 的下标像 2，也可能是 3。
7. 【?7】倍加性质：箭头上写的是 Rᵢ + Rᵢ·k，按倍加的意思应为 Rⱼ。
8. 【?8】范德蒙德例子：第三个因子写成了 (x₃−x₂)，按公式应为 (x₃−x₁)。
9. 【?9】2-3 习 1(1)：红笔先写了 n(n+1)/2，又推出 n(n−1)/2，两个都没划掉，按推导应是 n(n−1)/2。
10. 【?10】2-3 习 1(3)：两个子行列式原来只画了斜着排列的几个元素，这里用文字描述；红笔 ∏xᵢ 理解为把 xₙ! 改写成 ∏xᵢ。
11. 【?11】2-3 习 2(2)：「an-1」读成 aₙ−1（aₙ 减 1），不是 aₙ₋₁；aᵢ(aₙ−1) 的括号是转录时加的。
12. 【?12】2-3 习 2(8)：「an+1」读成 aₙ+1，不是 aₙ₊₁。这题原来写在 PDF 第 8 页顶部，这里按题号排在 2(6) 后面。
13. 【?13】2-4 习第 2 题：「(−1)^{mn} 6」里的 6 也可能是 b；原题条件不在笔记里。
14. 【?14】3.1 旁注「设 A B 都是 A 的逆矩阵」：按后面的推导应为「设 B、C 都是」。
15. 【?15】克拉默法则证明「（A*b）」：A* 和 b 中间有一个字看不清。
16. 【?16】Gemini 旁注：第一行 xᵢ 后面写的是 a₁（应为 aᵢ？）；第二行「xᵢb₁」那一项看不懂；行列式里 b 那一列每行都只写了 b。
17. 【?17】最后一句「有无多解」：是想写「有无穷多解」，还是「无解或有无穷多解」？
