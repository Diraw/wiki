# 群

一个非空集合 $S$ +一个二元运算 $*:S\times S\rightarrow S$

如果 $(S,*)$ 满足封闭性、结合律、单位元、逆元，则构成一个群

# 交换群

如果 $*$ 满足交换律

# 环

一个加法交换群上，再定义一个二元乘法运算

乘法满足封闭性、结合律、分配律，**但不一定有单位元、不一定可逆，不一定满足交换律**

# 域

乘法运算有单位元、可逆、满足交换律的环

例子：
1. 数域：$\mathbb{Q},\mathbb{R},\mathbb{C}$ 
2. 非数域：伽罗瓦域

---

**注**：

在许多抽象代数的教材中，通常**会默认环是带单位元的**，这是因为带单位元的环在理论上更丰富，并且许多重要的定理和结构都需要单位元的存在。然而，从最基本的定义来看，单位元并不是环的必要条件。

1. 带单位元的环：
	- $\mathbb{Z},\mathbb{Q},\mathbb{R},\mathbb{C}$：单位元是 $1$
	- 所有 $n\times n$ 矩阵的集合 $M_{n}[F]$：单位矩阵 $I_{n}$ 
	- 所有在域 $F$ 上的多项式集合 $F[x]$：单位元是常数多项式 $1$
2. 不带单位元的环：
	- 偶数整数集 $2\mathbb{Z}=\{\cdots,-4,-2,0,2,4,\cdots\}$

# 向量空间

向量空间 $V$：
1. 定义在域 $F$ 上的非空集合（依赖性）
2. 定义两种运算：
	1. 向量加法：$+:V\times V\rightarrow V$ （结合律，交换律，零向量，负向量）
	2. 标量乘法：$\cdot:F\times V\rightarrow V$ （对向量、标量加法的分配律，结合律，单位元）
3. 例子：
	1. 在实数域 $\mathbb{R}$ 上
		1. 所有 $n$ 维实数向量的集合：$\mathbb{R}^{n}$ 
		2. 所有次数不超过 $n$ 的多项式集合：$P_{n}(x)$ 
		3. 所有从某个区间 $[a,b]$ 到 $\mathbb{R}$ 的连续函数集合：$C[a,b]$ 

# 结合代数

同时具有向量空间和环两种结构的数学对象

一个在域 $F$ 上的结合代数 $A$ 是一个非空集合，满足：
1. $A$ 是一个在域 $F$ 上的向量空间：交换群 $(A,+)$ + 标量乘法 $\cdot_{s}:F\times A\rightarrow A$ 
2. 向量空间 $A$ 是一个环：交换群 $(A,+)$ + 代数乘法 $\cdot_{a}:A\times A\rightarrow A$ 
3. $A$ 的向量空间结构和环结构是兼容的：
	- 对于任意 $k\in F$ 和任意向量 $a,b\in A$，满足：$k\cdot_{s}(a\cdot_{a}b)=(k\cdot_{s}a)\cdot_{a}b=a\cdot_{a}(k\cdot_{s}b)$ 

例子：
1. 矩阵代数 $M_{n}(F)$ 
	- 是 $F$ 上的向量空间（矩阵加法、标量乘法）
	- 是环：矩阵加法（重合），乘法（封闭性、结合律、分配律，但不一定有逆元、但有单位元），不一定满足交换律
	- 兼容：$k(AB)=(kA)B=A(kB)$ 
2. 多项式代数 $F[x]$

# 多项式代数

一个在域 $F$ 上的变量 $x$ 的多项式，形如：$p(x)=a_{n}x^{n}+\cdots+a_{1}x+a_{0}$，所有多项式的集合记作 $F[x]$ 
- $a_{0},a_{1},\cdots,a_{n}$ 是来自域 $F$ 的系数
- $x$ 是一个形式变量（不变元），只是一个符号
- $n$ 是一个非负整数，如果 $a_{n}\neq_{0}$，则 $n$ 是多项式的次数

多项式代数 $F[x]$
1. 是 $F$ 上的向量空间（多项式加法，标量乘法）
2. 是环：多项式加法（重合）、乘法（封闭性、分配律，有单位元 常数多项式 $1$，但不一定有逆元），满足交换律
	- 只有非零常数多项式才有乘法逆元
	- 如果把环 $F[x]$ 扩展成有理函数域 $F(x)=\frac{p(x)}{q(x)}$，则所有非零多项式在 $F(x)$ 中具有乘法逆元
3. 兼容：对于任意标量 $k\in F$ 和任意多项式 $p(x),q(x)\in F[x]$，满足：$k\cdot(p(x)\cdot q(x))=(k\cdot p(x))\cdot q(x)=p(x)\cdot(k\cdot q(x))$ 

# 二次型

二次型是一个从矢量到标量的映射，$Q:\mathbb{R}^{n}\rightarrow \mathbb{R}$ 

矩阵表示：$Q(\mathbf{x})=\mathbf{x}^{T}A\mathbf{x}$，其中 $\mathbf{x}$ 是列向量，$A$ 为 $n\times n$ 的实对称矩阵，称为二次型的系数矩阵

# 线性型

对域 $F$ 上的向量空间 $V$，线性型（线性泛函）是一个从矢量到标量的映射，$f:V\rightarrow F$

满足：
- 可加性：$f(\mathbf{u}+\mathbf{v})=f(\mathbf{u})+f(\mathbf{v})$ 
- 齐次性：$f(c \mathbf{v})=cf(\mathbf{v})$ 
- 可合并为：$f(c_{1}\mathbf{u}+c_{2}\mathbf{v})=c_{1}f(\mathbf{u})+c_{2}f(\mathbf{v})$ 

例子：
1. $f(\mathbf{x})=f(x_{1},x_{2},x_{3})=2x_{1}-3x_{2}+x_{3}$ 是 $\mathbb{R}^{3}$ 上的一个线性型
2. 积分运算 $I(g)=\int_{a}^{b}g(t)\mathrm{d}t$ 是函数空间 $C[a,b]$ 上的一个线性型

# 双线性型

对域 $F$ 上的向量空间 $V$，双线性型是一个映射 $B:V\times V\rightarrow F$，满足
- $B(c_{1}\mathbf{u_{1}}_+c_{2}\mathbf{u_{2}},\mathbf{v})=c_{1}B(\mathbf{u_{1}},\mathbf{v})+c_{2}B(\mathbf{u_{2}},\mathbf{v})$ 
- $B(\mathbf{u},c_{1}\mathbf{v_{1}+c_{2}\mathbf{v_{2}}})=c_{1}B(\mathbf{u},\mathbf{v_{1}})+c_{2}B(\mathbf{u},\mathbf{v_{2}})$ 

常用 $B(\mathbf{v},\mathbf{v})$ 定义 $Q(\mathbf{v})$ 
- 欧氏空间中，定义 $B(\mathbf{u},\mathbf{v})=\mathbf{u}\cdot \mathbf{v}$，有 $Q(\mathbf{v})=B(\mathbf{v},\mathbf{v})=\mathbf{v}\cdot \mathbf{v}$
- 一般定义，如果 $B(\mathbf{u},\mathbf{v})=0$，则 $\mathbf{u}$ 和 $\mathbf{v}$ 在几何上是正交的

# 对称双线性型

满足：$B(\mathbf{u},\mathbf{v})=B(\mathbf{v},\mathbf{u})$ 

例子：
1. $\mathbb{R}^{n}$ 上的点积
2. 对 $A_{n\times x}$ 和 $\mathbf{x},\mathbf{y}\in \mathbb{R}^{n}$，定义 $B(\mathbf{x},\mathbf{y})=\mathbf{x^{T}}A\mathbf{y}$ 

---

如果如果空间中先得到了二次型 $Q$，且基域的特征不为 2（即 1+1≠0，对于 $\mathbb{R}$ 和 $\mathbb{C}$ 总是成立），那么由 $Q$ 唯一确定一个对称双线性型 $B$：

$$
\begin{aligned}
Q(\mathbf{u}+\mathbf{v})&=B(\mathbf{u}+\mathbf{v},\mathbf{u}+\mathbf{v})\\
&=B(\mathbf{v},\mathbf{v})+B(\mathbf{u},\mathbf{v})+B(\mathbf{v},\mathbf{u})+B(\mathbf{u},\mathbf{u})\\
&=B(\mathbf{v},\mathbf{v})+2B(\mathbf{u},\mathbf{v})+B(\mathbf{u},\mathbf{u})\\
&=Q(\mathbf{v})+2B(\mathbf{u},\mathbf{v})+Q(\mathbf{u})\\
\Rightarrow B(\mathbf{u},\mathbf{v})&=
\frac{1}{2}\left(Q(\mathbf{u}+\mathbf{v})-Q(\mathbf{u})-Q(\mathbf{v})\right)
\end{aligned}
$$

一个域 $F$ 的特征是指最小的整数 $n$，使得在 $F$ 中的单位元自身相加 $n$ 次得到 $0$
- 如果这样的正整数不存在，我们就说这个域的特征是 $0$ 
- 如果特征为 $2$，则 $2\cdot 1=0$，这意味着 $2$ 没有乘法逆元，$\frac{1}{2}$ 不成立

# Clifford 代数

设 $V$ 是域 $F$ 上的向量空间，并设 $Q:V\rightarrow F$ 是 $V$ 上的一个二次型

新定义乘法：对于任意 $\mathbf{v}\in V$，满足 $\mathbf{v}^{2}=Q(\mathbf{v})\cdot \mathbf{1}$ 
- $\mathbf{1}$：单位元

Clifford代数 $Cl(V,Q)$ 是一个由 $V$ 生成的结合代数
1. 是 $F$ 上的向量空间（向量加法，标量乘法）
2. 是环：向量加法（重合）、新定义的乘法（封闭性、分配律，有单位元 $\mathbf{1}$，但不一定有逆元），不一定满足交换律
3. 兼容：对于任意标量 $k\in F$ 和向量 $\mathbf{u},\mathbf{v}\in Cl(V,Q)$，满足：$k\cdot(\mathbf{uv})=(k\cdot \mathbf{u})\mathbf{v}=\mathbf{u}(k\cdot \mathbf{v})$ 

更一般的定义为张量代数 $T(V)$ 对由所有形如 $\mathbf{v}\otimes \mathbf{v}-Q(\mathbf{v})$ 的元素生成的双边理想 $I_{Q}$​ 的商代数：$Cl(V,Q)=T(V)/I_{Q}$ 

---

新定义乘法 $\mathbf{v}^{2}=Q(\mathbf{v})\cdot \mathbf{1}$ 的等价描述：$\{\mathbf{u},\mathbf{v}\}=2B(\mathbf{u},\mathbf{v})$ 
- 即从二次型 $Q$ 或对称双线型 $B$ 出发，都可以定义Clifford代数

$$
\begin{aligned}
Q(\mathbf{u}+\mathbf{v})&=(\mathbf{u}+\mathbf{v})^{2}\\
&=\mathbf{u}^{2}+\mathbf{uv}+\mathbf{vu}+\mathbf{v^{2}}\\
&=Q(\mathbf{u})+\mathbf{uv}+\mathbf{vu}+Q(\mathbf{v})\\
\Rightarrow \{\mathbf{u},\mathbf{v}\}&=\mathbf{uv}+\mathbf{vu}\\
&=Q(\mathbf{u}+\mathbf{v})-Q(\mathbf{u})-Q(\mathbf{v})\\
&=2B(\mathbf{u},\mathbf{v})
\end{aligned}
$$

---

对于两个向量的乘积 $\mathbf{uv}=\frac{1}{2}(\mathbf{uv}+\mathbf{vu})+\frac{1}{2}(\mathbf{uv}-\mathbf{vu})$ 

从代数考虑：
$\mathbf{uv}=\frac{1}{2}(\mathbf{uv}+\mathbf{vu})+\frac{1}{2}(\mathbf{uv}-\mathbf{vu})=\frac{1}{2}\{\mathbf{u},\mathbf{v}\}+\frac{1}{2}[\mathbf{u},\mathbf{v}]$
- 第一部分为对称部分，$\{\}$ 为反对易子
- 第二部分为反对称部分，$[]$ 为李括号，$[\mathbf{u},\mathbf{v}]$ 是交换子

从几何考虑：
$\mathbf{uv}=\frac{1}{2}(\mathbf{uv}+\mathbf{vu})+\frac{1}{2}(\mathbf{uv}-\mathbf{vu})=B(\mathbf{u},\mathbf{v})+\mathbf{u}\wedge \mathbf{v}$
- 第一部分是内积的Clifford体现
	- 告诉我们两个向量**有多少是“相同”的**，它们如何相互“对齐”，以及它们在共同方向上的作用
	- 它是一个**标量**，代表了**线性作用**或**投影**
- 第二部分是外积的Clifford体现，描述两个向量张成的有向平面区域
	- 告诉我们两个向量**有多少是“不同”的**，它们如何相互“正交”，以及它们共同定义了一个什么样的平面
	- 它是一个**双向量**，代表了**旋转作用**或**平面区域**

---

现从Clifford代数的几何性出发：

考虑Clifford代数的特殊乘法：
-  $\mathbf{v}^{2}=Q(\mathbf{v})\cdot \mathbf{1}$
- $\{\mathbf{u},\mathbf{v}\}=2B(\mathbf{u},\mathbf{v})$ 
- $B(\mathbf{u},\mathbf{v})=\frac{1}{2}(Q(\mathbf{u}+\mathbf{v})-Q(\mathbf{u})-Q(\mathbf{v}))$

在向量空间中，一个向量长度的平方是 $\lVert \mathbf{v}\lVert^{2}=\mathbf{v}\cdot \mathbf{v}$，这是一个二次型的特例 -> $Q(\mathbf{v})=\mathbf{v}^{T}I\mathbf{v}$，其中 $I$ 为单位矩阵

选择不同的二次型 $Q(\mathbf{v})$，实际上是在选择不同的度规，这个度规定义了向量空间中的“长度”和“角度”概念

而 $B(\mathbf{u},\mathbf{v})$ 就是由二次型 $Q(\mathbf{v})$ 导出的度规张量

考虑欧几里得空间：

$$
\begin{aligned}
Q(\mathbf{u}+\mathbf{v})&=\sum_{i=1}^{n}(u^{i}+v^{i})^{2}=\sum_{i=1}^{n}\left((u^{i})^{2}+2u^{i}v^{i}+(v^{i})^{2}\right)\\
&=\sum_{i=1}^{n}(u^{i})^{2}+2\sum_{i=1}^{n}u^{i}v^{i}+\sum_{i=1}^{n}(v^{i})^{2}\\
&=Q(\mathbf{u})+2\sum_{i=1}^{n}u^{i}v^{i}+Q(\mathbf{v})\\
\Rightarrow B(\mathbf{u},\mathbf{v})&=\frac{1}{2}(Q(\mathbf{u}+\mathbf{v})-Q(\mathbf{u})-Q(\mathbf{v}))=\sum_{i=1}^{n}u^{i}v^{i}
\end{aligned}
$$

欧几里得空间：
- $V=\mathbb{R}^{n}$
- $Q(\mathbf{v})=\mathbf{v}^{T}I\mathbf{v}$，其中 $I$ 为单位矩阵
- 度规矩阵 $G=I$ 
- 度规张量 $B(\mathbf{u},\mathbf{v})=\sum_{i=1}^{n}u^{i}v^{i}=u^{i}v_{i}$ 
- 对于欧氏几何，长度是正的，并且满足勾股定理
- 可记作 $Cl(\mathbb{R}^{n},Q)$ 或 $Cl_{n}(\mathbb{R})$ 或 $Cl(n,0)$ 

闵可夫斯基空间：
- $V=\mathbb{R}^{1,3}$
- $Q(\mathbf{v})=(v^{0})^{2}-(v^{1})^{2}-(v^{2})^{2}-(v^{3})^{2}$（使用西海岸约定）
- 度规矩阵 $G=\text{diag}(1,-1,-1,-1)$ 
- 度规张量 $B(\mathbf{u},\mathbf{v})=g_{\mu \nu}u^{\mu}v^{\nu}$ 
- 对于狭义相对论中的时空几何，长度平方可以是正的（类时向量）、负的（类空向量）或零（类光向量）
- 一般记作 $Cl_{1,3}(\mathbb{R})$ 或 $Cl(1,3)$ 

其他伪欧几里得空间：
- 度规矩阵 $G =\text{diag}(\underbrace{1, \ldots, 1}_{p \text{ 次}}, \underbrace{-1, \ldots, -1}_{q \text{ 次}})$

以上均为度规矩阵为对角矩阵的例子。如果空间的基对于二次型不是正交的，度规矩阵就会出现非对角分量

例如广义相对论中，在旋转的黑洞附近，使用Kerr度规

$$
G=\begin{pmatrix}
g_{tt}&g_{t\phi}&0&0 \\
g_{\phi t}&g_{\phi \phi}&0&0 \\
0&0&g_{rr}&0 \\
0&0&0&g_{\theta\theta} \\
\end{pmatrix}
$$
- $g_{tt}=-\left(1-\frac{2Mr}{\Sigma}\right)$
- $g_{t\phi}=g_{\phi t}=-\frac{2aMr\sin ^{2}\theta}{\Sigma}$ 
- $g_{rr}=\frac{\Sigma}{\Delta}$ 
- $g_{\theta\theta}=\Sigma$
- $g_{\phi \phi}=\left(r^{2}+a^{2}+\frac{2Mra^{2}\sin ^{2}\theta}{\Sigma}\right)\sin ^{2}\theta$
- $M$：黑洞质量
- $a$：黑洞角动量参数
- $\Sigma=r^{2}+a^{2}\cos ^{2}\theta$ 
- $\Delta=r^{2}-2Mr+a^{2}$ 

这意味着在 Boyer-Lindquist 坐标系下，时间轴和 $\phi$ 轴（旋转方向）是**非正交的**

# Dirac 代数

本质上是闵可夫斯基时空上的 Clifford 代数 $Cl_{1,3}(\mathbb{R})$ 的复化版本

因为在量子力学中，波函数本身就是复数，粒子的量子态通常用复向量表示。因此，为了描述量子粒子的动力学，其代数结构自然也需要扩展到复数域

如果选择闵可夫斯基空间的标准基 $\{\mathbf{e_{0}},\mathbf{e_{1}},\mathbf{e_{2}},\mathbf{e_{3}}\}$，使得 $Q(\mathbf{e_{0}})=1,Q(\mathbf{e_{1}})=-1,Q(\mathbf{e_{1}})=-1,Q(\mathbf{e_{1}})=-1$，并且 $B(\mathbf{e_{\mu}},\mathbf{e_{\nu}})=g_{\mu \nu}$ 

$\mathbf{uv}+\mathbf{vu}=2B(\mathbf{u},\mathbf{v})$ 得 $\mathbf{e^{\mu}e^{\nu}}+\mathbf{e^{\nu}e^{\mu}}=2g^{\mu \nu}$，将 $\mathbf{e^{\mu}}$ 替换为 $\gamma^{\mu}$ 

通常记为 $\mathcal{D}$ 或 $Cl_{1,3}(\mathbb{C})$，是满足以下**反对易关系**的生成元 $\gamma^{\mu}$ 所生成的结合代数：

$$
\{\gamma^{\mu},\gamma^{\nu}\}=\gamma^{\mu}\gamma^{\nu}+\gamma^{\nu}\gamma^{\mu}=2g^{\mu \nu}\mathbf{1}
$$

- $\mu,\nu$：时空坐标
- $g^{\mu \nu}$：度规张量（逆变形式）
- $\mathbf{1}$：代数的单位元

这些生成元 $\gamma^{\mu}$ 被称为**狄拉克 $\gamma$ 矩阵**（Dirac matrices）。但它们本身不是矩阵，而是代数的抽象生成元。在实际应用中，通常会寻找这些生成元的矩阵表示：

**Dirac 表示**：

$$
\gamma^{0}=\begin{pmatrix}
I_{2}&0 \\
0&-I_{2}
\end{pmatrix},
\gamma^{i}=\begin{pmatrix}
0&\sigma^{i} \\
-\sigma^{i}&0
\end{pmatrix}(i=1,2,3)
$$
其中 $I_{2}$ 是 $2\times 2$ 单位矩阵，$\sigma^{i}$ 是泡利矩阵

$$
\sigma^{1}=\begin{pmatrix}
0&1 \\
1&0
\end{pmatrix},\sigma^{2}=\begin{pmatrix}
0&-i \\
i&0
\end{pmatrix},\sigma^{3}=\begin{pmatrix}
1&0 \\
0&-1
\end{pmatrix}
$$

**外尔表示 (Weyl Representation) 或手征表示 (Chiral Representation)**：

$$
\gamma^{0}=\begin{pmatrix}
0&I_{2} \\
I_{2}&0
\end{pmatrix},\gamma^{i}=\begin{pmatrix}
0&\sigma^{i} \\
-\sigma^{i}&0
\end{pmatrix}
$$
这种表示在处理手征对称性时非常有用，因为它可以使得手征投影算符 $\gamma^{5}$ 具有对角形式

**马约拉纳表示 (Majorana Representation)**：
- 所有的 $\gamma^{\mu}$ 都是纯虚数矩阵。这种表示在描述马约拉纳费米子（粒子就是其自身的反粒子）时非常有用

以上表示都是等价的，可以通过酉变换相互转换

---

狄拉克代数是一个 $2^{4}=16$ 维的复向量空间。它的一个基可以由以下 16 个元素构成：
1. 标量：$\mathbf{1}$ 或 $I_{4}$（1个）
	1. 对应不依赖于方向的标量，如粒子的质量、电荷等
2. 向量：$\gamma^{\mu}$（4个）
	1. 对应时空向量，如粒子的四动量 $p^{\mu}$、电磁场的四电流 $j^{\mu}$ 等
3. 双向量或二形式（2-form）：$\sigma^{\mu \nu}=\frac{i}{2}[\gamma^{\mu},\gamma^{\nu}]$（6个）
	1. 更基本应该写成 $\gamma^{\mu}\gamma^{\nu}$ 且 $\mu<\nu$ 
	2. 对应时空中的二阶反对称张量，如电磁场张量 $F^{\mu \nu}$、洛伦兹群的生成元（描述旋转和洛伦兹加速）、自旋角动量算符 $S^{\mu \nu}=\frac{i\hbar}{4}[\gamma^{\mu},\gamma^{\nu}]$ 等
4. 三向量或三形式或伪向量：$\gamma^{0}\gamma^{1}\gamma^{2},\gamma^{0}\gamma^{1}\gamma^{3},\gamma^{0}\gamma^{2}\gamma^{3},\gamma^{1}\gamma^{2}\gamma^{3}$ （4个）
	1. 对应于轴向量或轴流，如轴向电流
5. 伪标量 (Pseudoscalar)：$\gamma^{5}=i\gamma^{0}\gamma^{1}\gamma^{2}\gamma^{3}$（1个）
	1. 对应宇称变换下改变符号的标量。在粒子物理中，它与手征对称性破缺、轴向异常等现象相关

---

核心应用：**Dirac方程**

在量子力学中，薛定谔方程描述了非相对论粒子。但当粒子速度接近光速时，需要一个相对论性的量子力学理论

最初的尝试是Klein-Gorden方程 $\partial_{\mu}\partial^{\mu}\Psi-m^{2}\Psi=0$，但它存在如负概率密度和无法自然地描述粒子的自旋等问题

狄拉克在1928年提出了一个革命性的方程，旨在解决这些问题。他希望找到一个满足以下条件的方程：
- 满足洛伦兹协变性，即在不同惯性系下形式不变
- 像薛定谔方程一样，在时间上是一阶导数，以保证概率密度为正定
- 能够自然地包含电子的自旋-1/2性质

Dirac方程：$(i\gamma^{\mu}\partial_{\mu}-m)\psi=0$
- $\partial_{\mu}=\frac{\partial}{\partial x^{\mu}}$ 
- $\gamma^{\mu}$：**狄拉克 $\gamma$ 矩阵**
- $m$：粒子的质量
- $\psi$：**狄拉克旋量**，它是一个四分量复值列向量，$\psi=\begin{pmatrix}\psi_{1} \\ \psi_{2} \\ \psi_{3} \\ \psi_{4}\end{pmatrix}$
	- 狄拉克旋量是狄拉克代数的表示空间中的元素。即 $\gamma^{\mu}$ 矩阵作用于这个四维向量空间，将一个旋量映射到另一个旋量，这个四维空间就是狄拉克代数的一个表示。

普通旋量（Spinors）是 $SO(3)$群的二重覆盖群 $SU(2)$的表示

狄拉克旋量是洛伦兹群 $SO(1,3)$ 的二重覆盖群 $Spi n(1,3)$ 的表示，**当物理系统进行 $2\pi$ 旋转时，旋量会改变其符号，只有在进行 $4\pi$ 旋转后才能恢复到初始状态**。这种变换性质正合适描述自旋1/2粒子（如电子）自旋。

考虑绕 $z$ 轴的旋转算符：$R_{z}(\theta)=e^{-i\theta S_{z}/\hbar}$ 
- $S_{z}$：沿 $z$ 轴的角动量算符，由广义相对论 $S^{\mu \nu}=\frac{i\hbar}{4}[\gamma^{\mu},\gamma^{\nu}]$

对于狄拉克旋量，$S_{z}=S^{12}=\frac{\hbar}{2}\begin{pmatrix}\sigma_{z}&0 \\ 0&\sigma_{z}\end{pmatrix}$，$R_{z}(\theta)=\begin{pmatrix}e^{-i\frac{\theta}{2}\sigma_{z}}&0 \\ 0&e^{-i\frac{\theta}{2}\sigma_{z}}\end{pmatrix}$，其中 $e^{-i\frac{\theta}{2}\sigma_{z}}=\cos\left( \frac{\theta}{2} \right)I_{2}-i\sin\left( \frac{\theta}{2} \right)\sigma_{z}$ 

现考虑 $2\pi$ 旋转，$e^{-i\frac{\theta}{2}\sigma_{z}}=\cos\left( \frac{2\pi}{2} \right)I_{2}-i\sin\left( \frac{2\pi}{2} \right)\sigma_{z}=(-1)I_{2}-i(0)\sigma_{z}=-I_{2}$，得

$$
R_{z}(2\pi)=\begin{pmatrix}
-I_{2}&0 \\
0&-I_{2}
\end{pmatrix}
=-I_{4}$$

则，$4\pi$ 旋转 $R_{z}(4\pi)=I_{4}$

---

狄拉克方程的解不仅包含正能量解（对应电子），还包含负能量解。狄拉克最初提出了“狄拉克海”的概念来解释这些负能量态，认为所有负能量态都被填满了。后来，费曼等人重新解释了这些负能量解，认为它们对应于具有正能量但电荷相反的粒子，即反粒子。

---

在狄拉克代数的基中，**伪标量 $\gamma^{5}$ 尤其重要**。它不仅在宇称变换下改变符号，更在粒子物理中扮演着**手征算符**的角色。通过 $\gamma^{5}$，我们可以将狄拉克旋量分解为左手旋量和右手旋量分量。在弱相互作用中，只有左手旋量参与，这导致了宇称不守恒的现象，是粒子物理标准模型中的一个核心特征。

$\gamma^{5}$ 的两个代数性质：
- 幂等性平方：$(\gamma^{5})^{2}=1$ 
- 反对易性：$\{\gamma^{5},\gamma^{\mu}\}=0$ （$\gamma^{5}$ 与任何一个狄拉克 $\gamma$ 矩阵都反对易）

定义手征投影算符：$P_{L}=\frac{1-\gamma^{5}}{2}$ 和 $P_{R}=\frac{1+\gamma^{5}}{2}$ 
- 幂等性：$P_{L}^{2}=P_{L}$，$P_{R}^{2}=P_{R}$
- 正交性：$P_{L}P_{R}=0$
- 完备性：$P_{L}+P_{R}=1$ 

任何狄拉克旋量 $\psi$ 都可以被分解为两个手征分量：$\psi=\mathbf{1}\psi=(P_{L}+P_{R})\psi=\psi_{L}+\psi_{R}$
- 称 $\psi_{L}$，$\psi_{R}$ 为左手、右手旋量

这些手征分量都是 $\gamma^{5}$ 的本征态：
- 左手旋量：$\gamma^{5}\psi_{L}=\gamma^{5}\frac{1-\gamma^{5}}{2}\psi=\frac{\gamma^{5}-1}{2}\psi=-\psi_{L}$ （本征值为 $-1$）
- 右手旋量：$\gamma^{5}\psi_{R}=\gamma^{5}\frac{1+\gamma^{5}}{2}\psi=\frac{\gamma^{5}+1}{2}\psi=\psi_{R}$ （本征值为 $+1$）

**宇称变换 (P)** 是一种空间反演操作，它将空间坐标 $\mathbf{x}$ 变为 $-\mathbf{x}$，而时间 $t$ 不变。对于狄拉克旋量 $\psi(t,\mathbf{x})$，其宇称变换定义为：

$$
\psi(t,\mathbf{x})\xrightarrow{P}\psi'(t, \mathbf{x})=\gamma^{0}\psi(t,-\mathbf{x})
$$

现考察 $\gamma^{5}$ 在宇称变换下的行为。

在狄拉克理论中，宇称算符 $P$ 在旋量空间中的作用被表示为 $\gamma^{0}$，有 $P=\gamma^{0}$。一个算符 $O$ 在宇称变换下的行为由 $POP^{-1}$ 给出。

由于 $(\gamma^{0})^{2}=\mathbf{1}$，所以 $P^{-1}=\gamma^{0}$ $\Rightarrow$ $P\gamma^{5}P^{-1}=\gamma^{0}\gamma^{5}\gamma^{0}=\gamma^{0}(-\gamma^{0}\gamma^{5})=-(\gamma^{0})^{2}\gamma^{5}=-\gamma^{5}$
 
因此，$\gamma^{5}$ **在宇称变换下改变符号，它是一个伪标量**

这意味着，手征投影算符 $P_{L}$​ 和 $P_{R}$​ 在宇称变换下会互换：
- $PP_{L}P^{-1}=P\frac{1-\gamma^{5}}{2}P^{-1}=\frac{1-P\gamma^{5}P^{-1}}{2}=\frac{1-(-\gamma^{5})}{2}=P_{R}$ 
- $PP_{R}P^{-1}=P_{L}$ 

所以，宇称变换会将左手旋量变为右手旋量，将右手旋量变为左手旋量：
- $P\psi_{L}P^{-1}=P(P_{L}\psi)P^{-1}=(PP_{L}P^{-1})(P\psi P^{-1})=P_{R}(\gamma^{0}\psi(t,-\mathbf{x}))=\psi'_{R}$ 
- $P\psi_{R} P^{-1}=P_{L}(\gamma^{0}\psi(t,\mathbf{x}))$ 

在粒子物理的标准模型中，**弱相互作用**（例如，电子和中微子之间的相互作用）的物理过程由一个特殊的**弱电流**来描述。这个电流具有一个独特的结构，被称为 **V-A (Vector minus Axial-vector) 形式**：

$$
J_{\mu}=\bar{\psi}\gamma^{\mu}(\mathbf{1}-\gamma^{5})\psi
$$

其中 $\bar{\psi}=\psi ^{\dagger}\gamma^{0}$ 是狄拉克伴随旋量

这个表达式中的因子 $(\mathbf{1}-\gamma^{5})$ 正好是 $2P_{L}$。这意味着弱相互作用的电流可以写成：

$$
J^{\mu}=2\bar{\psi}\gamma^{\mu}P_{L}\psi=2\bar{\psi}\gamma^{\mu}\psi_{L}
$$

这个形式清楚地表明，**弱相互作用只与狄拉克旋量的左手分量 $\psi_{L}$​ 发生作用**

现在我们考察这个弱电流在宇称变换下的行为。一个理论如果遵守宇称对称性，那么它的相互作用项在宇称变换下必须保持不变。

弱电流 $J^{\mu}$ 宇称变换之前写作：

$$
J^{\mu}=\bar{\psi}\gamma^{\mu}(\mathbf{1}-\gamma^{5})\psi
$$

在宇称变换下，$\bar{\psi}(t,\mathbf{x})\xrightarrow{P}\bar{\psi}'(t,\mathbf{x})=\bar{\psi}(t,-\mathbf{x})\gamma^{0}$ ，且 $\gamma^{\mu}$ 的变换为 $P\gamma^{\mu}P^{-1}=\gamma^{0}\gamma^{\mu}\gamma^{0}$：
- $P\gamma^{0}P^{-1}=\gamma^{0}$ 
- $P\gamma^{i}P^{-1}=-\gamma^{i}$（对于空间指标 $i=1,2,3$）

同时，我们知道 $P\gamma^{5}P^{-1}=-\gamma^{5}$

考虑电流的变换：

$$
\begin{aligned}
J^{\mu}\xrightarrow{P}J^{\mu}_{P}&=\bar{\psi}'(t,\mathbf{x})\gamma^{\mu}(\mathbf{1}-\gamma^{5})\psi'(t,\mathbf{x})\\
&=\bar{\psi}(t,-\mathbf{x})\gamma^{0}\gamma^{\mu}(\mathbf{1}-\gamma^{5})\gamma^{0}\psi(t,-\mathbf{x})\\
&=\bar{\psi}(t,-\mathbf{x})(\gamma^{0}\gamma^{\mu}\gamma^{0}-\gamma^{0}\gamma^{\mu}\gamma^{5}\gamma^{0})\psi(t,-\mathbf{x})\\
&=\bar{\psi}(t,-\mathbf{x})(\gamma^{0}\gamma^{\mu}\gamma^{0}+\gamma^{0}\gamma^{\mu}\gamma^{0}\gamma^{5})\psi(t,-\mathbf{x})\\
&=\bar{\psi}(t,-\mathbf{x})(\gamma^{0}\gamma^{\mu}\gamma^{0})(\mathbf{1}+\gamma^{5})\psi(t,-\mathbf{x})
\end{aligned}
$$

这个结果清晰地表明了两点：

1. **协变性**：流的四矢量分量部分 $\gamma^{\mu}$ 变成了 $\gamma^{0}\gamma^{\mu}\gamma^{0}$，这正是四矢量在宇称变换下应有的变换行为。
2. **对称性破缺**：内部的手征结构从 V-A $(\mathbf{1}-\gamma^{5})$ 变成了 V+A $(\mathbf{1}+\gamma^{5})$，**由于变换后的物理定律（由 V+A 描述）与原始的物理定律（由 V-A 描述）具有不同的代数形式，因此我们说这个理论不具备宇称对称性**，或者说，弱相互作用破坏了宇称对称性。

也就是按理来说，宇称变换前后，弱电流算符始终应该只与左手旋量作用。但是现在出现的情况是，**宇称变换后，弱电流算符仍然跑去和宇称变换之前是左手旋量、现在宇称变换后已经是右手的旋量结合去了**。算符本身不能因为粒子的改变（粒子从左手变成右手）而改变（算符从偏爱左手变成偏爱右手），但现在却改变了，因为定律本身变了，所以对称性被破坏了。

---

附标准的教科书论证方法：

弱电流 $J^{\mu}$ 是一个矢量分量 $V^{\mu}=\bar{\psi}\gamma^{\mu}\psi$ 和一个轴矢量分量 $A^{\mu}=\bar{\psi}\gamma^{\mu}\gamma^{5}\psi$ 的差，即 $J^{\mu}=V^{\mu}-A^{\mu}$ 

考虑一个典型的弱相互作用项，例如 $J^{\mu}J_{\mu}$。在宇称变换下，它的行为如下：

$$
J^{\mu}J_{\mu}=(V^{\mu}-A^{\mu})(V_{\mu}-A_{\mu})=V^{\mu}V_{\mu}-V^{\mu}A_{\mu}-A^{\mu}V_{\mu}+A^{\mu}A_{\mu}
$$

在宇称变换下：

- $V^{\mu}V_{\mu}$ 是一个标量，其形式不变。
- $A^{\mu}A_{\mu}$​ 是一个标量，其形式不变。
- $V^{\mu}A_{\mu}$ 和 $A^{\mu}V_{\mu}$ 是伪标量，它们在宇称变换下会改变符号。

因此，整个相互作用项 $J^{\mu}J_{\mu}$ 在宇称变换下变为：

$$
\begin{aligned}
(V^{\mu}-A^{\mu})(V_{\mu}-A_{\mu})&=(V^{\mu}V_{\mu}-V^{\mu}A_{\mu}-A^{\mu}V_{\mu}+A^{\mu}A_{\mu})\\
\xrightarrow{P}(V^{\mu}V_{\mu}+V^{\mu}A_{\mu}+A^{\mu}V_{\mu}+A^{\mu}A_{\mu})&=(V^{\mu}+A^{\mu})(V_{\mu}+A_{\mu})
\end{aligned}
$$
由于 $(V^{\mu}-A^{\mu})(V_{\mu}-A_{\mu})\neq(V^{\mu}+A^{\mu})(V_{\mu}+A_{\mu})$，这意味着**弱相互作用的拉格朗日密度在宇称变换下改变了其代数形式**（从 V-A 耦合变为 V+A 耦合）。这种形式的改变直接证明了弱相互作用不遵守宇称对称性。

