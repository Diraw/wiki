标题：对距离度量的信息含量进行排序

论文提出了一种统计检验方法，可用于评估在使用**两种不同距离度量时所保留的相对信息量**，并判断它们是等价的、相互独立的，还是其中一个比另一个更具信息

---

在机器学习中有一个问题是，如何从数据量相对稀少、但每个数据点却拥有大量特征的数据库中，提取出有用的信息

在化学信息学和材料科学中，分子和材料可以用大量特征来描述，但由于预测量子性质所需的计算或实验成本极高，数据库的规模往往受到严重限制

对于样本不多的数据库而言，可用特征维度太多不算是好事，因为深度神经网络通常需要**大量独立且彼此无关的样本**才能有效训练

因此，在训练模型之前，一般会先进行数据清洗

现有的方法

---

从数学的角度来看，样本的不同特征维度对应一个相同的高维样本空间的不同度量

论文引入了"**信息失衡**" **information imbalance** 这一指标，用于量化某一距离度量相对于另一距离度量的相对信息含量

**定义**：**空间排名函数** $r_{ij}^{A}$ 表示点 $j$ 相对于点 $i$ 在 $A$ 空间中的**排名**
- 如果空间中有 $N$ 个点，则最近邻为 1，最远邻为 $N$ 

<img src="https://img.diraw.top/i/u/2026/01/08/xrtbj1.png" width="50%" />

观察下图发现，对于点 $i$
- 其在 $x$ 维度中最近的点 $k$，在 $y$ 维度中的 $r_{ik}^{y}=35$ 
- 其在 $y$ 维度中最近的点 $j$，在 $x$ 维度中的 $r_{ik}^{x}=7$ 

<img src="https://img.diraw.top/i/u/2026/01/08/xsmjk4.png" width="50%" />

我们发现 $r_{ik}^{x} \ll r_{ik}^{y}$，即在空间 $y$ 中测得的第1个邻点在空间 $x$ 中的排名，远小于在空间 $x$ 中测得的第1个邻点在空间 $y$ 中的排名

而我们可以很直观的发现空间 $y$ 对特征点的描述优于空间 $x$，这就是本篇论文的核心所在

---

**定义**：**Copula变量**

$$
c_{ij}^A = \int_0^{d_{ij}^A} p_A(w|i) dw \approx \frac{r_{ij}^A}{N} \quad
$$

$p_{A}(w|i)$ 是局部距离分布的概率密度函数，表示在点 $i$ 的周围，采样到一个距离为 $w$ 的点的概率是多少
- 不是指以点 $i$ 为圆心，$\omega$ 为半径画圆，有点落入圆的概率
- 而是指以点 $i$ 为圆心，在距离为 $\omega$ 的地方发现点的概率（相当于落在圆周上的概率）

而 $\int_0^{d_{ij}^A} p_A(w|i)dw$ 表示才是以点 $i$ 为圆心，$d_{ij}^A$ 为半径画圆，在圆内发现点的概率（对概率进行累加）

这个积分的含义为：**在所有的样本点中，有多大比例的点，它们离中心点 $i$ 的距离比点 $j$ 还要近**

这刚好等于 $\frac{r_{ij}^{A}}{N}$（考虑大数定律），表示点 $j$ 在 $A$ 空间中相对于点 $i$ 的排名，在整个排名列表的第几之几

---

**定义**："**信息失衡**" **information imbalance**

$$\Delta(A \to B) = 2 \lim_{\epsilon \to 0} \langle c_B | c_A = \epsilon \rangle \quad$$
表示已知在空间 $A$ 中两点距离极近（$c_A$ 趋于 $0$）时，这两点在空间 $B$ 中的平均排名（$c_B$）是多少

**系数 2**：因为 Copula 变量 $c_B$ 在 $[0, 1]$ 上均匀分布，其随机状态下的期望值是 $1/2$，为了让 $\Delta$ 在独立时等于 $1$，所以乘以 $2$ 进行归一化

**离散情况**：在有限数据集中，当 $r_{ij}^A = 1$（$j$ 是 $i$ 在 $A$ 中的第一近邻）时，计算 $j$ 在 $B$ 中的排名 $r_{ij}^B$ 的平均值：

$$\Delta(A \to B) \approx \frac{2}{N} \langle r_{ij}^B | r_{ij}^A = 1 \rangle = \frac{2}{N^2} \sum_{i=1}^N r_{i, n1(i)}^B \quad$$

- 其中 $n1(i)$ 表示在空间 $A$ 中点 $i$ 的第一近邻的索引

执行步骤：

1. **确定 A 空间的邻居关系**：对于数据集中的每一个点 $i$，计算它到其他所有点的距离 $d_A$。排序后，找到距离 $i$ 最近的那个点，把它记作 $j$（即 $r_{ij}^A = 1$）
2. **跨空间查询排名**：保持点 $i$ 和点 $j$ 的配对关系不变，转到 B 空间。在 B 空间里，计算点 $i$ 到其他所有点的距离 $d_B$，然后看刚才那个点 $j$ 现在排在第几名，这个排名就是 $r_{i, n1(i)}^B$ 
3. **求和与平均**：对所有的点 $i$ 重复上述操作。会得到 $N$ 个排名数字，把这些数字加起来，除以 $N^2$，再乘以 2。

考虑两个极端情况：

1. 完全等价 ($A \equiv B$)：如果 $A$ 和 $B$ 信息完全一致，那么 $A$ 的最近邻必然也是 $B$ 的最近邻，即 $r_{ij}^B = 1$。

$$\Delta \approx \frac{2}{N} \cdot 1 \approx 0 \quad (\text{当 } N \to \infty) \quad$$

2. 完全独立（正交）：如果 $B$ 与 $A$ 无关，那么 $A$ 的最近邻在 $B$ 中的排名是随机的，其排名的期望值为 $N/2$。

$$\Delta \approx \frac{2}{N} \cdot \frac{N}{2} = 1 \quad$$

因此，$\Delta \in [0, 1]$。$\Delta$ 越趋于 0，表示 $A$ 对 $B$ 的描述越充分。

---

论文考虑了一个三维高斯分布，其中 $z$ 方向的标准差远远小于 $x$ 和 $y$ 方向的，度量均为欧式度量

直觉上：

1. **空间 $A:d_{xyz}$ 和空间 $B:d_{xy}$ 描述信息几乎相同**
	- 因此，点在 $xyz$ 中的排名与点在 $xy$ 中的排名几乎相等
	- **在散点图 (a) 中兑现 $45^\circ$ 对角线分布**，这意味着一个点在 $A$ 空间是第几近邻，在 $B$ 空间基本也是第几近邻
	- **在分布图 (b) 中**，条件概率 $p(r^{xy} | r^{xyz} = 1)$ 和 $p(r^{xyz} | r^{xy} = 1)$ 都表现为**极高的尖峰**，且集中在 $r=1$ 附近，这说明无论从哪个方向预测，第一近邻几乎永远保持不变
	- 此时，$\Delta(A\to B)\approx\Delta(B\to A)\approx 0$ 
2. **空间 $A:xy$ 比空间 $B:x$ 包含更多信息**
	- **在散点图 (c) 中**，点群不再是对角线，而是呈现出一种**非对称的扩散**
		- 散点的偏离表示**信息的流向**
	- **在分布图 (b) 中**：
		- **红线** ($p(r^x | r^{xy} = 1)$)：依然有一个很高的尖峰。这意味着如果知道二维坐标 ($xy$)，你能很准地找到 $x$ 坐标上的近邻
		- **蓝线** ($p(r^{xy} | r^x = 1)$)：峰值塌陷，分布变得平坦。这意味着如果仅知道 $x$ 坐标，很难找回在 $xy$ 平面上的近邻，因为丢失了 $y$ 的信息
	- 此时，$0<\Delta(A\to B)<\Delta(B\to A)<1$ 
3. **空间 $A:x$ 与空间 $B:y$ 相互正交**
	- **在散点图 (e) 中**，点完全**均匀地铺满**了整个正方形区域。这说明在 $x$ 轴离得近的点，在 $y$ 轴上的排名完全是随机的
	- **在分布图 (f) 中**，两条曲线都变成了**水平直线** 
	- 此时，$\Delta(A\to B)\approx\Delta(B\to A)\approx 1$ 

<img src="https://img.diraw.top/i/u/2026/01/08/zhc2wi.png" width="100%" />

- 第一行图表示，一个点相对于另一个点在两个维度的排名（一共 $N$ 个点，图上有 $N(N-1)$ 个散点）
- 第二行图表示，第1排名的点在另一个维度属于第几排名的概率密度（遍历所有 $N$ 个点取平均）

<img src="https://img.diraw.top/i/u/2026/01/08/zib9mj.png" width="100%" />

---

使用python进行仿真模拟：

<img src="https://img.diraw.top/i/u/2026/01/08/10gdpuo.png" width="100%" />

```python
import numpy as np
import matplotlib.pyplot as plt
from scipy.spatial.distance import pdist, squareform
from scipy.stats import rankdata


def get_ranks(dist_matrix):
    """计算距离矩阵中每个点的排名"""
    ranks = np.zeros_like(dist_matrix)
    for i in range(dist_matrix.shape[0]):
        # rankdata 使用 'min'，最近邻排名为1（排除自身0距离）
        ranks[i] = rankdata(dist_matrix[i], method="min")
    return ranks


# 生成数据 (3D 高斯)
np.random.seed(42)
N = 1000  # 样本数量 
x = np.random.normal(0, 1, N)
y = np.random.normal(0, 1, N)
z = np.random.normal(0, 0.1, N)  # z轴方差小
data_3d = np.stack([x, y, z], axis=1)

space_xyz = data_3d
space_xy = data_3d[:, :2]
space_x = data_3d[:, :1]
space_y = data_3d[:, 1:2]


def simulate_plots_line(S1, S2, title, ax_scatter, ax_line, labels):
    d1 = squareform(pdist(S1, "euclidean"))
    d2 = squareform(pdist(S2, "euclidean"))

    # 填充对角线为无穷大，防止自身成为最近邻
    np.fill_diagonal(d1, np.inf)
    np.fill_diagonal(d2, np.inf)

    r1 = get_ranks(d1)
    r2 = get_ranks(d2)

    # 第一行：散点图
    ax_scatter.scatter(r1.flatten(), r2.flatten(), s=1, color="gray", alpha=0.3)
    ax_scatter.set_title(f"{title}: Scatter")
    ax_scatter.set_xlabel(f"rank in {labels[0]}")
    ax_scatter.set_ylabel(f"rank in {labels[1]}")

    # 第二行：条件分布折线图
    # 找到 S1 空间中每个点的第1近邻索引
    n1_S1 = np.argmin(d1, axis=1)
    # 该近邻在 S2 中的排名
    ranks_in_S2 = np.array([r2[i, n1_S1[i]] for i in range(N)])

    # 找到 S2 空间中每个点的第1近邻索引
    n1_S2 = np.argmin(d2, axis=1)
    # 该近邻在 S1 中的排名
    ranks_in_S1 = np.array([r1[i, n1_S2[i]] for i in range(N)])

    # 计算分布 (横坐标从1开始)
    max_r = int(N * 0.1)  # 只画前10%的排名，因为峰值在头部
    bins = np.arange(1, max_r + 2) - 0.5  # 确保点落在整数中心

    for r_data, color, label in zip(
        [ranks_in_S2, ranks_in_S1],
        ["red", "blue"],
        [f"p(r_{labels[1]}|r_{labels[0]}=1)", f"p(r_{labels[0]}|r_{labels[1]}=1)"],
    ):
        counts, bin_edges = np.histogram(r_data, bins=bins, density=True)
        bin_centers = (bin_edges[:-1] + bin_edges[1:]) / 2
        # 绘图：横坐标从1开始
        ax_line.plot(bin_centers, counts, color=color, label=label, linewidth=1.5)
        ax_line.fill_between(bin_centers, counts, color=color, alpha=0.1)

    ax_line.set_xlim(1, max_r)
    ax_line.set_ylim(0, None)
    ax_line.set_xlabel("rank (r)")
    ax_line.set_ylabel("p(r)")
    ax_line.legend(fontsize="x-small")
    ax_line.set_title(f"{title}: PDF")


# 开始绘图
fig, axes = plt.subplots(2, 3, figsize=(18, 10))

simulate_plots_line(
    space_xyz, space_xy, "Equivalence", axes[0, 0], axes[1, 0], ["xyz", "xy"]
)
simulate_plots_line(space_xy, space_x, "Inclusion", axes[0, 1], axes[1, 1], ["xy", "x"])
simulate_plots_line(
    space_x, space_y, "Orthogonality", axes[0, 2], axes[1, 2], ["x", "y"]
)

plt.tight_layout()
plt.show()
```

