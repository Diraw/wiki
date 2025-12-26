# 非独立联合概率分布

在本科阶段的概率论课程中，通常会涉及到**非独立联合概率分布**的内容，但侧重点往往在于**分析**已知分布，而不是从零开始**构造**复杂的非独立分布。

## 二元正态分布

这是本科阶段最核心的非独立分布案例。课程会讲解如何通过相关系数 $\rho$ 来构造两个随机变量之间的依赖关系。

- 如果 $X$ 和 $Y$ 是独立的标准正态分布，通过线性变换（如 $Y' = \rho X + \sqrt{1-\rho^2}Z$）可以构造出具有特定相关性的联合分布。
- 你会学习到其联合概率密度函数（PDF），其中相关系数 $\rho$ 直接决定了分布的**形状**和变量间的线性相关性。

## 边缘分布与条件分布的乘积

这是构造非独立分布最直观的方法，也是本科必考点：

$$\begin{aligned}f(x, y) = f_{X}(x) \cdot f_{Y|X}(y|x)\end{aligned}$$

通过给定一个变量的边缘分布和另一个变量相对于它的条件分布，就可以构造出联合分布。

## 随机变量的函数变换 (Jacobian 矩阵)

选择两个相互独立的随机变量 $U$ 和 $V$，它们的联合密度函数 $f_{U,V}(u, v) = f_U(u) \cdot f_V(v)$ 是已知的（通常选最简单的均匀分布或正态分布）。

建立变换关系：定义两个函数 $g_1, g_2$，令：$X = g_1(U, V)$, $Y = g_2(U, V)$ ，为了使 $X$ 和 $Y$ 非独立，通常让 $g_1$ 和 $g_2$ 同时依赖于同一个源变量。

计算反函数：将 $U, V$ 反解为 $X, Y$ 的函数（假设变换是一一对应的）：$u = h_1(x, y), \quad v = h_2(x, y)$

根据概率密度变换公式，构造出的 $X$ 和 $Y$ 的联合密度函数为：

$$\begin{aligned}f_{X,Y}(x, y) = f_{U,V}(h_1(x, y), h_2(x, y)) \cdot |J|\end{aligned}$$

其中 $|J|$ 是 Jacobian 行列式的绝对值，定义为：

$$\begin{aligned}J = \det \begin{pmatrix} \frac{\partial u}{\partial x} & \frac{\partial u}{\partial y} \\ \frac{\partial v}{\partial x} & \frac{\partial v}{\partial y} \end{pmatrix}\end{aligned}$$

### 实例：从独立分布构造非独立分布

假设我们有两个独立的标准正态分布变量 $U, V \sim N(0, 1)$。我们想构造两个**具有相关性**的变量 $X, Y$。

**步骤 1：定义变换（线性组合构造相关性）**

设定一个相关系数 $\rho \in (-1, 1)$：

- $X = U$
- $Y = \rho U + \sqrt{1-\rho^2} V$
- (这里 $Y$ 同时包含了 $U$ 和 $V$，因此 $X$ 和 $Y$ 必然相关)

**步骤 2：反解变量**

- $u = x$
- $v = \frac{y - \rho x}{\sqrt{1-\rho^2}}$

**步骤 3：计算 Jacobian 行列式**

$$\begin{aligned}\frac{\partial u}{\partial x} = 1, \quad \frac{\partial u}{\partial y} = 0\end{aligned}$$

$$\begin{aligned}\frac{\partial v}{\partial x} = \frac{-\rho}{\sqrt{1-\rho^2}}, \quad \frac{\partial v}{\partial y} = \frac{1}{\sqrt{1-\rho^2}}\end{aligned}$$

$$\begin{aligned}J = (1 \cdot \frac{1}{\sqrt{1-\rho^2}}) - (0 \cdot \frac{-\rho}{\sqrt{1-\rho^2}}) = \frac{1}{\sqrt{1-\rho^2}}\end{aligned}$$

**步骤 4：写出最终的联合分布**

因为 $U, V$ 独立且为正态分布，它们的联合密度是：

$$\begin{aligned}f_{U,V}(u, v) = \frac{1}{2\pi} e^{-\frac{u^2 + v^2}{2}}\end{aligned}$$

代入 $u, v$ 的表达式和 $|J|$：

$$\begin{aligned}f_{X,Y}(x, y) = \frac{1}{2\pi \sqrt{1-\rho^2}} \exp\left( -\frac{1}{2} \left[ x^2 + \left( \frac{y - \rho x}{\sqrt{1-\rho^2}} \right)^2 \right] \right)\end{aligned}$$

整理后，这就是标准的二元正态分布密度函数。

---
# 超矩形

$B = [x_1, y_1]$ 是数轴上的一个**线段**。

$B = [x_1, y_1] \times [x_2, y_2]$ 代表平面上的一个**矩形**。它的横坐标在 $x_1$ 到 $y_1$ 之间，纵坐标在 $x_2$ 到 $y_2$ 之间。

$B = [x_1, y_1] \times [x_2, y_2] \times [x_3, y_3]$ 是日常生活中见到的**长方体**。

当 $d > 3$ 时，虽然我们无法直观想象它的样子，但在数学上它依然遵循同样的规则，被称为**超矩形**。

$B = [0, 1]^d$ 表示这个超矩形被限制在一个单位超正方体内。也就是说，所有的坐标值都在 0 到 1 之间。

$$\begin{aligned}[0, 1]^d = \{ (p_1, p_2, \dots, p_d) : 0 \le p_i \le 1, \forall i = 1, \dots, p_d \}\end{aligned}$$

应用：

- 测度论：勒贝格测度
- 计算几何：空间索引（k-d 树, R 树）、正交范围查询、碰撞检测（AABB 理论）

# C-体积

C-体积定义为概率分布函数在多维空间中**对一个超矩形所定义的概率质量**

**一维情况：** 如果 $F(x)$ 是分布函数，区间 $[x_1, y_1]$ 的概率是 $F(y_1) - F(x_1)$

**二维情况：** 对于矩形 $[x_1, y_1] \times [x_2, y_2]$，其 $C$-体积（即落在这个矩形内的概率）计算如下：

$$\begin{aligned}V_C(B) = C(y_1, y_2) - C(x_1, y_2) - C(y_1, x_2) + C(x_1, x_2)\end{aligned}$$

这个公式的逻辑是：取大矩形的右上角值，减去上方和左方的多余部分，由于左下角部分被减了两次，所以要加回来一次（容斥原理）。

对于 $d$-维超矩形 $B = \prod_{i=1}^d [x_i, y_i]$，$C$-体积定义为函数 $C$ 在该矩形顶点处的**符号加权求和**：

$$\begin{aligned}V_C(B) = \sum_{\mathbf{v} \in \text{vertices}(B)} \text{sgn}(\mathbf{v}) C(\mathbf{v})\end{aligned}$$

其中，顶点 $\mathbf{v} = (v_1, \dots, v_d)$ 的符号权重由下式决定：

- 如果 $v_i = x_i$ 的分量个数是偶数，则符号为正 ($+1$)。
- 如果 $v_i = x_i$ 的分量个数是奇数，则符号为负 ($-1$)。
# Copula的定义

一个 $d$-维 Copula 是一个映射 $C: [0, 1]^d \rightarrow [0, 1]$，其为**多元联合累积分布函数**（CDF），满足以下性质：

- **边界性（Grounded）：** 如果任何一个参数 $u_i = 0$，则 $C(\mathbf{u}) = 0$。

$$\begin{aligned}C(u_1, \dots, 0, \dots, u_d) = 0\end{aligned}$$

- **边缘均匀性（Uniform Margins）：** 如果除了 $u_i$ 以外的所有参数都为 1，则函数值等于 $u_i$。

$$\begin{aligned}C(1, \dots, u_i, \dots, 1) = u_i\end{aligned}$$

- **d-递增性（d-increasing）：** 对于任意超矩形 $B = [x_1, y_1] \times \dots \times [x_d, y_d] \subseteq [0, 1]^d$（其中 $x_i \le y_i$），该矩形上的 $C$-体积是非负的。
	- 这保证了它对应的概率密度函数是非负的。

与普通联合CDF的区别：

- **普通联合 CDF**：描述的是具体的**观测值**（如：身高 $\leq180cm$ 和体重 $\leq80kg$）同时发生的概率。
- **Copula**：描述的是**分位数**（如：身高处于前 $10\%$ 且体重处于前 $20\%$）同时发生的概率。
	- $\Rightarrow$ Copula相当于**去除量纲后**的联合CDF

# Sklar 定理

令 $H$ 为一个具有边缘分布函数 $F_1, \dots, F_d$ 的 $d$-维联合分布函数。那么，存在一个 Copula 函数 $C$，使得对所有 $x_1, \dots, x_d$：

$$\begin{aligned}H(x_1, \dots, x_d) = C(F_1(x_1), \dots, F_d(x_d))\end{aligned}$$

反之亦然，如果 $F_1, \dots, F_d$ 是连续分布函数，那么 $C$ 是唯一确定的，且可以通过下式构造：

$$\begin{aligned}C(u_1, \dots, u_d) = H(F_1^{-1}(u_1), \dots, F_d^{-1}(u_d))\end{aligned}$$

其中 $u_i = F_i(x_i)$，即 $u_i$ 是数据经过累积分布函数（CDF）变换后的值（也就是将任意分布的数据映射到了 $[0, 1]$ 区间）。

# Copula 出现前后对比

**目标：** 构建一个双变量分布 $(X, Y)$，满足以下两个条件：

1. **边缘分布**：$X$ 和 $Y$ 都要服从**指数分布**（即 $F(x) = 1 - e^{-x}$）。
2. **相关性**：两者要有**很强**的正相关性（比如 $\rho > 0.8$）。

我们来看看在 Copula 理论普及之前和之后，数学家是如何完成这个任务的，以及推导过程有何不同。

## Copula 出现之前

**方法：硬凑公式**

在 Sklar 定理成为主流工具之前，数学家如果想让边缘分布保持指数分布，通常采用**FGM (Farlie-Gumbel-Morgenstern) 构造法**。

他们的思路是：在独立分布 $F(x)F(y)$ 的基础上加一个小小的**扰动项**。

假设我们希望联合分布 $H(x, y)$ 是这样的形式：

$$\begin{aligned}H(x, y) = F(x)F(y) \cdot [1 + \alpha (1 - F(x))(1 - F(y))]\end{aligned}$$

其中 $\alpha$ 是关联参数。

代入指数分布 $F(x) = 1 - e^{-x}$：

$$\begin{aligned}H(x, y) = (1 - e^{-x})(1 - e^{-y}) [1 + \alpha e^{-x} e^{-y}]\end{aligned}$$

**验证边缘分布**：

为了保证这个公式合法，必须验证当 $y \to \infty$ 时，$H(x, y)$ 是否变回了 $F(x)$。

$$\begin{aligned}H(x, \infty) = (1 - e^{-x})(1 - 0) [1 + \alpha e^{-x} \cdot 0] = 1 - e^{-x}\end{aligned}$$

**计算相关系数 $\rho$：**

经过计算（中间过程放在[附录](Copula理论#一个经典FGM问题)），对于 FGM 分布，相关系数 $\rho$ 和参数 $\alpha$ 的关系是：

$$\begin{aligned}\rho = \frac{\alpha}{4}\end{aligned}$$

数学悲剧发生了：

为了保证 $H(x, y)$ 是一个合法的概率分布（即概率密度 $h(x,y) \ge 0$），参数 $\alpha$ 必须满足 $|\alpha| \le 1$。

这意味着：

$$\begin{aligned}|\rho| \le \frac{1}{4} = 0.25\end{aligned}$$

**结论：** 在旧的数学框架下，你费尽心机凑出的这个公式，**最高只能描述 0.25 的相关性**。

## Copula 出现之后

**方法：模块化组装**

现在我们有了 Sklar 定理。我们不再需要去凑一个整体公式，而是像搭积木一样。

**选择组件**：

- **积木 A (边缘分布):** $u = F(x) = 1 - e^{-x}$, $v = F(y) = 1 - e^{-y}$
- 积木 B (Copula): 我们选一个能描述强相关的 Copula，比如 Clayton Copula（甚至可以描述 $\rho \to 1$ 的情况）：

$$\begin{aligned}C(u, v) = (u^{-\theta} + v^{-\theta} - 1)^{-1/\theta}, \quad \theta > 0\end{aligned}$$

根据 Sklar 定理 $H(x, y) = C(F(x), F(y))$，我们直接把 $u$ 和 $v$ 的表达式塞进去：

$$\begin{aligned}H(x, y) = \left[ (1 - e^{-x})^{-\theta} + (1 - e^{-y})^{-\theta} - 1 \right]^{-1/\theta}\end{aligned}$$

**这就结束了！这就是我们要的解析式。**

## 对比：概率密度函数 (PDF) 的推导

为了让你感受微积分层面的差异，我们对比一下求 **概率密度函数 (PDF)** $f(x,y) = \frac{\partial^2 H}{\partial x \partial y}$ 的过程。

### 传统方法

你必须对那个复杂的、硬凑出来的整体大公式直接求偏导。如果公式本身很丑（为了满足边缘条件凑得很复杂），求导过程会是一场噩梦，而且很容易出错。

### Copula 方法

利用链式法则，PDF 的公式是通用的、解耦的：

$$\begin{aligned}f(x, y) = \underbrace{c(u, v)}_{\text{依赖结构密度}} \times \underbrace{f_X(x) \cdot f_Y(y)}_{\text{边缘密度}}\end{aligned}$$

其中 $c(u, v) = \frac{\partial^2 C}{\partial u \partial v}$ 是 Copula 的密度。

**这一公式的物理意义极强：**

- **$f_X(x) f_Y(y)$**：这是假设两者**独立**时的概率密度。
- **$c(u, v)$**：这是一个**修正系数**（权重）。
    - 如果某处 $c(u, v) > 1$，说明这里发生的概率比独立时更高（正相关区域）。
    - 如果某处 $c(u, v) < 1$，说明这里发生的概率比独立时更低。

# 常见的 Copula 家族结构

## 椭圆 Copula (Elliptical Copulas)

这类 Copula 来源于椭圆分布（如正态分布、t分布）。它们保留了相关系数矩阵的概念。

1. Gaussian Copula (高斯 Copula):
$$\begin{aligned}C_{\rho}^{Gauss}(u, v) = \Phi_{\rho}(\Phi^{-1}(u), \Phi^{-1}(v))\end{aligned}$$
    - $\Phi_{\rho}$ 是相关系数为 $\rho$ 的二元标准正态分布函数。
    - $\Phi^{-1}$ 是标准正态分布的逆函数。
    - _特点：_ 没有尾部相关性（极端情况下变量趋于独立）。
2. t-Copula：源于多元 t-分布。
    - 特点：具有尾部相关性（Tail Dependence）。即使相关系数为0，在极端值时也可能表现出相关，这在金融危机建模中至关重要。

## 阿基米德 Copula (Archimedean Copulas)

这类 Copula 不是从分布函数推导出来的，而是通过一个**生成元函数 (Generator Function)** $\phi$ 构建的。结构非常优雅。

一般形式：

$$\begin{aligned}C(u_1, \dots, u_d) = \phi^{-1}(\phi(u_1) + \dots + \phi(u_d))\end{aligned}$$

其中 $\phi: [0, 1] \rightarrow [0, \infty]$ 是一个连续、严格递减的凸函数，且 $\phi(1)=0$。

常见的阿基米德 Copula 包括：

- Clayton Copula: 下尾相关性强（适合模拟**大跌时一起跌**的市场）。
$$\begin{aligned}\phi(t) = \frac{1}{\theta}(t^{-\theta} - 1)\end{aligned}$$
- **Gumbel Copula:** 上尾相关性强。
- **Frank Copula:** 对称依赖结构。

# 尾部依赖

这是学习 Copula 结构时必须掌握的一个指标。它衡量的是：**当一个变量出现极端值时，另一个变量也出现极端值的概率。**

定义下尾依赖系数 $\lambda_L$：

$$\begin{aligned}\lambda_L = \lim_{u \rightarrow 0^+} P(Y \le G^{-1}(u) | X \le F^{-1}(u)) = \lim_{u \rightarrow 0^+} \frac{C(u, u)}{u}\end{aligned}$$

- 对于 Gaussian Copula，$\lambda_L = 0$（除非 $\rho=1$）。
- 对于 Clayton Copula，$\lambda_L = 2^{-1/\theta} > 0$。

这意味着如果用 Gaussian Copula 去模拟金融危机，你会严重低估风险，因为你忽略了极端的共同崩溃概率。

# 附录

## 一个经典FGM问题

这是一个经典的 **Farlie-Gumbel-Morgenstern (FGM)** 分布族的一个特例。

$$\begin{aligned}H(x, y) = (1 - e^{-x})(1 - e^{-y}) [1 + \alpha e^{-x} e^{-y}]\end{aligned}$$

在这个问题中，边缘分布是标准的指数分布。我们可以通过以下步骤计算相关系数 $\rho$。

### 1. 确定边缘分布 (Marginal Distributions)

首先，我们需要找出 $X$ 和 $Y$ 各自的累积分布函数 (CDF) 和概率密度函数 (PDF)。

令 $y \to \infty$，则 $e^{-y} \to 0$。边缘分布 $F_X(x)$ 为：

$$\begin{aligned}F_X(x) = H(x, \infty) = (1 - e^{-x})(1 - 0)[1 + 0] = 1 - e^{-x}\end{aligned}$$

同理，

$$\begin{aligned}F_Y(y) = 1 - e^{-y}\end{aligned}$$

可以看出，$X$ 和 $Y$ 服从参数为 $\lambda=1$ 的**标准指数分布**。

对于标准指数分布：

- **期望 (Mean):** $E[X] = 1, \quad E[Y] = 1$
- **方差 (Variance):** $\text{Var}(X) = 1, \quad \text{Var}(Y) = 1$
- **标准差:** $\sigma_X = 1, \quad \sigma_Y = 1$

### 2. 计算联合概率密度函数

联合概率密度函数 $h(x, y)$ 是联合 CDF 的混合二阶偏导数：

$$\begin{aligned}h(x, y) = \frac{\partial^2 H(x, y)}{\partial x \partial y}\end{aligned}$$

通过对给定的 $H(x, y)$ 求导（或者利用 FGM 分布的通用公式），我们可以得到：

$$\begin{aligned}h(x, y) = e^{-x}e^{-y} \left[ 1 + \alpha (2e^{-x} - 1)(2e^{-y} - 1) \right]\end{aligned}$$

### 3. 计算 $E[XY]$

相关系数公式为：

$$\begin{aligned}\rho = \frac{E[XY] - E[X]E[Y]}{\sigma_X \sigma_Y}\end{aligned}$$

由于已知 $E[X]=1, E[Y]=1, \sigma_X=1, \sigma_Y=1$，公式简化为：

$$\begin{aligned}\rho = E[XY] - 1\end{aligned}$$

我们需要计算 $E[XY]$：

$$\begin{aligned}E[XY] = \int_{0}^{\infty} \int_{0}^{\infty} xy \cdot h(x, y) \, dx \, dy\end{aligned}$$

将 $h(x, y)$ 代入：

$$\begin{aligned}E[XY] = \int_{0}^{\infty} \int_{0}^{\infty} xy e^{-x}e^{-y} \left[ 1 + \alpha (2e^{-x} - 1)(2e^{-y} - 1) \right] dx dy\end{aligned}$$

这个积分可以拆分为两部分：

第一部分 (独立项):

$$\begin{aligned}\int_{0}^{\infty} x e^{-x} dx \cdot \int_{0}^{\infty} y e^{-y} dy = E[X]E[Y] = 1 \cdot 1 = 1\end{aligned}$$

第二部分 (交叉项):

$$\begin{aligned}\alpha \left( \int_{0}^{\infty} x e^{-x} (2e^{-x} - 1) dx \right) \left( \int_{0}^{\infty} y e^{-y} (2e^{-y} - 1) dy \right)\end{aligned}$$

由于 $x$ 和 $y$ 的积分形式完全相同，我们只需要计算其中一个积分 $I$：

$$\begin{aligned}I = \int_{0}^{\infty} x e^{-x} (2e^{-x} - 1) dx = \int_{0}^{\infty} (2x e^{-2x} - x e^{-x}) dx\end{aligned}$$

利用积分公式 $\int_{0}^{\infty} x e^{-kx} dx = \frac{1}{k^2}$：

- 对于第一项 ($k=2$): $\int_{0}^{\infty} 2x e^{-2x} dx = 2 \cdot \frac{1}{2^2} = \frac{1}{2}$
- 对于第二项 ($k=1$): $\int_{0}^{\infty} x e^{-x} dx = 1$

所以：

$$\begin{aligned}I = \frac{1}{2} - 1 = -\frac{1}{2}\end{aligned}$$

因此，第二部分的值为：

$$\begin{aligned}\alpha \cdot I \cdot I = \alpha \left(-\frac{1}{2}\right) \left(-\frac{1}{2}\right) = \frac{\alpha}{4}\end{aligned}$$

合并两部分：

$$\begin{aligned}E[XY] = 1 + \frac{\alpha}{4}\end{aligned}$$

### 4. 最终结果

计算协方差 (Covariance)：

$$\begin{aligned}\text{Cov}(X, Y) = E[XY] - E[X]E[Y] = \left(1 + \frac{\alpha}{4}\right) - 1 = \frac{\alpha}{4}\end{aligned}$$

计算相关系数 (Correlation Coefficient)：

$$\begin{aligned}\rho = \frac{\text{Cov}(X, Y)}{\sigma_X \sigma_Y} = \frac{\alpha / 4}{1 \cdot 1}\end{aligned}$$

结论：

$$\begin{aligned}\rho = \frac{\alpha}{4}\end{aligned}$$

**注意**：为了保证 $h(x, y)$ 是合法的概率密度函数（即始终非负），参数 $\alpha$ 的取值范围通常**限制**在 $[-1, 1]$ 之间。因此，这种分布的相关系数 $\rho$ 的范围被**限制**在 $[-0.25, 0.25]$ 之间。

