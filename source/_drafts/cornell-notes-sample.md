---
title: 康奈尔笔记排版样例
tags:
  - 线性代数
categories:
  - 学习笔记
---

这是康奈尔笔记排版的样例，内容取自线代笔记第 5 页（11.5，2-1、2-2）：左边是线索栏，右边是笔记栏，最下面是总结栏。

## 2-1 行列式

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

## 2.2 行列式的性质

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
</div>

<div class="cornell-summary">

**总结**（示例，可换成你自己的）：行列式的性质大多能用「按行展开」证明；转置不改变行列式的值，所以对行成立的性质对列也成立。代数余子式只比余子式多一个符号 $(-1)^{i+j}$，不乘元素本身。

</div>
