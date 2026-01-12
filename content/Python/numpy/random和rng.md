在 NumPy 的发展历程中，随机数生成系统经历了从 **旧的 Legacy 接口** 到 **新的 Generator (RNG) 接口** 的重大转变（自 NumPy 1.17 版本开始）。

# 核心差异：Legacy vs. Generator

目前 NumPy 的随机数功能分为两套并行的系统：

- **Legacy Random (`np.random.*`):** 基于 `RandomState` 类。这是老用户最熟悉的 `np.random.seed(0)` 和 `np.random.rand()` 模式。
- **New Generator (`np.random.Generator`):** 通过 `np.random.default_rng()` 创建。这是目前官方**强烈建议**使用的现代接口。

新接口采用了“解耦”的设计思想，将随机数的**生成逻辑**与**概率分布转换**分开了：

1. **BitGenerator (引擎):** 负责生成原始的 64 位或 128 位随机比特流（如 PCG64 算法）。
2. **Generator (用户接口):** 负责将比特流转换为特定的分布（如正态分布、均匀分布）。

# 对比

## 现代 RNG (推荐)

- **优点**：
    - **确定性更好**：在大型项目中，通过传递 `rng` 对象，可以精确控制每一步的随机性，不受外部库干扰。
    - **性能优越**：生成速度更快，内存占用更低。
    - **扩展性强**：支持 `BitGenerator` 的更换（如需要极高性能或极高安全性的特殊场景）。
- **缺点**：
    - 需要稍微多写一行代码（实例化 `rng`）。
    - 旧代码迁移需要改动函数名。
## 传统 Random (已弃用)

- **优点**：
    - **简单直接**：随调随用，适合在交互式窗口（如 Jupyter 临时测试）做极简演示。
    - **兼容性**：大量陈旧教程和老旧代码库仍在使用。
- **缺点**：
    - **污染全局环境**：这在工程实践中是非常危险的行为。
    - **算法老化**：不再享受 NumPy 后续的性能优化。

# 常用函数对比

| **功能**       | **旧接口 (Legacy)**               | **新接口 (Generator/RNG)**             |
| ------------ | ------------------------------ | ----------------------------------- |
| **初始化**      | `np.random.seed(seed)`         | `rng = np.random.default_rng(seed)` |
| **0-1 均匀分布** | `np.random.rand(d0, d1)`       | `rng.random((d0, d1))`              |
| **标准正态分布**   | `np.random.randn(d0, d1)`      | `rng.standard_normal((d0, d1))`     |
| **整数随机数**    | `np.random.randint(low, high)` | `rng.integers(low, high)`           |
| **随机洗牌**     | `np.random.shuffle(arr)`       | `rng.shuffle(arr)`                  |
| **随机采样**     | `np.random.choice(arr, size)`  | `rng.choice(arr, size)`             |

> **注意：** 新接口的 `random()` 和 `standard_normal()` 接收一个 `tuple` 作为形状参数（例如 `(3, 2)`），而旧接口的 `rand()` 和 `randn()` 是直接接收多个位置参数（例如 `3, 2`）

# 现代推荐用法：`default_rng`

使用新接口的第一步是初始化一个 `Generator` 实例。

```python
import numpy as np

# 初始化 Generator
# seed 可以是一个整数，也可以不传（默认使用系统熵）
rng = np.random.default_rng(seed=42)

# 生成数据
data = rng.standard_normal(10)  # 生成10个标准正态分布随机数
ints = rng.integers(low=0, high=10, size=5)  # 生成0-9之间的随机整数
```

**为什么选择 `default_rng`？**

- **更好的统计特性:** 默认使用的 **PCG64** 算法比旧版的 Mersenne Twister (MT19937) 更快、更节省内存，且在统计检验中表现更优。
- **线程安全:** 新接口的对象实例不依赖全局状态。在并行计算（如多线程或多进程）中，每个线程持有一个独立的 `Generator` 副本，互不干扰。
- **灵活性:** 某些分布在新接口下性能大幅提升，且 API 更加一致（例如 `integers` 替代了 `randint`）。

# 进阶：并行计算中的随机性

在科学计算（如 Monte Carlo 模拟或神经网络训练）中，如果你需要在多个进程中并行生成随机数，新接口提供了 `SeedSequence` 来确保各进程间的随机序列相互独立。
## 为什么并行时不能随便 seed=123、124、125…

很多人会这么写：

```python
rngs = [np.random.default_rng(123+i) for i in range(n_proc)]
```

这在“工程上经常看起来没问题”，但隐患是：

- 对某些算法/生成器来说，**相邻种子**可能导致内部状态在某些维度上相关（相关性是否显著取决于生成器与使用方式）。
- 更现实的问题：**你很难管理复现实验** 
    - 进程数量变了、任务分配方式变了，seed 的分配顺序一变，结果就不一致
    - 甚至同一个程序因为调度顺序不同导致“哪个任务拿到了哪个 seed”不稳定

`SeedSequence` 的目标是：  **把“复现”与“并行拆流”从你的任务调度里剥离出来。**

## 它怎么“确保独立”？

这里要非常诚实地说：在随机数里，“确保独立”严格意义上很难像数学证明一样 100% 保证。`SeedSequence` 的承诺更接近于：

- 用足够大的状态空间（现代生成器如 PCG64 状态非常大）
- 用良好的混合策略把不同 child 的初始化状态分散到状态空间中
- 从工程和统计意义上，让不同流**几乎不可能重叠**且**相关性可忽略**

所以你在并行 Monte Carlo、bootstrap、随机初始化权重、数据增强等任务里，把每个 worker 的 RNG 用 `spawn` 得到的子种子初始化，是目前 NumPy 推荐且非常稳妥的做法。

## 正确用法：让“任务身份”决定 seed，而不是“运行时顺序”

并行最容易踩的坑是：任务调度顺序不稳定 → 你把 seed 按顺序发给 worker → 结果漂移。

更稳的方式是：**按任务 ID 派生**。

### 方式 A：提前 spawn 出足够多的子种子，按任务索引取

```python
from numpy.random import SeedSequence, default_rng

ss = SeedSequence(12345) # 不变，纯函数式初始化，只由12345决定
task_seeds = ss.spawn(n_tasks) # 不变，spawn是确定性的，只由ss和n_tasks决定
def run_task(i):
	rng = default_rng(task_seeds[i]) # 同一个task中，rng每次都在变
	return rng.normal(size=1000).mean()
```

只要同时满足：

1. **`rootseed` 不变**
2. **`n_tasks` 不变**

那么，**就能复现**
### 方式 B：层级 spawn（推荐用于“嵌套并行/多层循环”）

比如实验有 `epoch` 和 `worker` 两层：

```python
root = SeedSequence(12345)
epoch_seqs = root.spawn(num_epochs)
for e, epoch_ss in enumerate(epoch_seqs):
	worker_seqs = epoch_ss.spawn(num_workers)
	rngs = [default_rng(s) for s in worker_seqs]
```

---

```python
from numpy.random import SeedSequence, default_rng
root = SeedSequence(12345)
print(type(root))
num_epochs=1
num_workers=2
epoch_seqs = root.spawn(num_epochs)
print(epoch_seqs)
for e, epoch_ss in enumerate(epoch_seqs):
    worker_seqs = epoch_ss.spawn(num_workers)
    print(worker_seqs)
    rngs = [default_rng(s) for s in worker_seqs]
```

```
<class 'numpy.random.bit_generator.SeedSequence'>

[SeedSequence( entropy=12345, spawn_key=(0,), )]

[SeedSequence( entropy=12345, spawn_key=(0, 0), ), 
 SeedSequence( entropy=12345, spawn_key=(0, 1), )]
```

