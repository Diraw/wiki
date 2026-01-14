全局解释器锁（Global Interpreter Lock，简称 GIL）是 Python 编程语言（特指 CPython 参考实现）中最具争议、最被误解，同时也最核心的技术特征之一。

自 Python 诞生以来的三十余年间，GIL 作为 CPython 内存管理和线程安全模型的基础，既成就了 Python 在单线程脚本和胶水语言领域的霸主地位，也成为了其在多核并发计算时代面临的最大技术债务 。

# 锁的设计哲学

在 CPython 中，每个 Python 对象都有一个 `ob_refcnt` 字段记录引用次数。一旦归零，内存立即回收。这种机制简单高效，但在多线程下并不安全。

在多线程环境下，如果两个线程同时对同一个对象执行 `del` 操作（即 `ob_refcnt--`），且没有锁保护，就可能发生**竞态条件（Race Condition）**，导致计数器计算错误，进而引发内存泄漏或程序崩溃。

为了解决上述问题，CPython 选择了粗粒度锁（GIL）：**强制规定在任何一个 Python 进程内，无论有多少个 CPU 核心，同一时刻只能有一个线程处于“解释执行 Python 字节码”的状态。**

# GIL 的具体实例

GIL 的存在导致 Python 多线程在处理不同类型任务时表现出**截然不同的性能特征**。

## CPU 密集型任务的并行度为零

对于纯计算任务，多线程不仅无法利用多核加速，反而**因为锁竞争（GIL Context Switching）导致比单线程更慢**。

**代码：**

```python
import time
import threading

def cpu_bound_task(n):
    """纯 CPU 计算任务：递减计数"""
    while n > 0:
        n -= 1

COUNT = 100_000_000

# 1. 单线程
start = time.time()
cpu_bound_task(COUNT)
print(f"单线程: {time.time() - start:.4f} seconds")

# 2. 多线程 (2个线程，各跑一半任务)
t1 = threading.Thread(target=cpu_bound_task, args=(COUNT // 2,))
t2 = threading.Thread(target=cpu_bound_task, args=(COUNT // 2,))

start = time.time()
t1.start()
t2.start()
t1.join()
t2.join()
print(f"多线程: {time.time() - start:.4f} seconds")
```

**输出**：

```
单线程: 3.2728 seconds
多线程: 3.3046 seconds
```

多线程版本通常比单线程版本慢或者持平。这是因为两个线程在单个 CPU 核心上争抢 GIL，产生了额外的上下文切换开销，却无法并行执行。

## I/O 密集型任务的伪并行

当 Python 线程执行 I/O 操作（如读写文件、网络请求、time.sleep）时，它会**主动释放 GIL**。这使得 Python 多线程在 I/O 密集型场景下非常有效。

**代码：**

```python
import time
import threading

def io_bound_task(sec):
    """模拟 I/O 等待"""
    time.sleep(sec)

# 1. 单线程执行 (预期 2 秒)
start = time.time()
io_bound_task(1)
io_bound_task(1)
print(f"单线程 I/O: {time.time() - start:.4f} seconds")

# 2. 多线程执行 (预期 1 秒)
t1 = threading.Thread(target=io_bound_task, args=(1,))
t2 = threading.Thread(target=io_bound_task, args=(1,))

start = time.time()
t1.start(); t2.start()
t1.join(); t2.join()
print(f"多线程 I/O: {time.time() - start:.4f} seconds")
```

**输出**：

```
单线程 I/O: 2.0017 seconds
多线程 I/O: 1.0015 seconds
```

多线程版本耗时几乎减半。这是因为线程 A 在 sleep 时释放了 GIL，线程 B 立即获得 GIL 并运行，实现了并发等待。

## 护送效应（Convoy Effect）

当 CPU 密集型线程（如后台压缩数据）与 I/O 密集型线程（如 UI 响应）共存时，CPU 线程会频繁霸占 GIL，导致 I/O 线程唤醒后难以抢到锁，造成 UI 卡顿或网络延迟抖动。

# 传统规避方案

在 PEP 703 之前，为了利用多核，最通用的方案是 multiprocessing。

**多进程（Multiprocessing）：以空间换时间**

**代码：**

```python
import time
from multiprocessing import Pool

def cpu_bound_task(n):
    while n > 0:
        n -= 1

if __name__ == "__main__":
    COUNT = 100_000_000
    tasks = [COUNT // 2, COUNT // 2]
    with Pool(processes=2) as p:
        start = time.time()
        p.map(cpu_bound_task, tasks)
        end = time.time()
        print(f"多进程: {end - start:.4f} seconds")
```

**输出**：

```
多进程: 1.7697 seconds
```

相比多线程，多进程能利用多核 CPU，速度显著提升（通常快 1.8-2 倍）。但代价是内存占用翻倍以及进程间通信（IPC）的高开销 。

**注意**：

1. **保留了 `if __name__ == '__main__':`**，这在 Windows 系统上是运行多进程**必须**的，否则会陷入无限递归创建子进程的死循环
2. 原因：
	- **Windows 的生成（Spawn）机制**： 在 Windows 上，Python 无法像 Linux 那样直接克隆父进程。它必须启动一个全新的 Python 解释器，然后把你的 `.py` 文件重新导入（Import）一遍，以便让子进程知道有哪些函数可以运行。
	- **递归爆炸**：
		- 主进程运行代码，遇到 `Pool(2)`。
		- 主进程启动子进程 A。
		- 子进程 A 为了运行，会重新导入你的 `.py` 文件。
		- **如果没有保护**，子进程 A 在导入时也会执行到 `Pool(2)` 这行代码。
		- 于是子进程 A 又去启动子进程 B……
		- 循环往复，直到你的系统资源（内存/进程数）耗尽，电脑直接卡死或崩溃。

# 革命性突破：PEP 703 与自由线程 Python

## 下载无 GIL 的 Python

[官网](https://www.python.org/downloads/windows/)下载 installer，选择自定义安装，在 `Advanced Options` 中，勾选 `free-threaded` 版本

<img src="https://img.diraw.top/i/u/2026/01/14/10ef7n6.png" width="75%" />

然后在安装目录下会有两个python解释器，**选择带 `t` 的版本**

<img src="https://img.diraw.top/i/u/2026/01/14/10f6fn2.png" width="75%" />

**验证**：

```bash
& E:/Python/py314t/python3.14t.exe -X gil=0 -c "import sys; print(f'解释器路径: {sys.executable}'); print(f'GIL 是否已禁用: {not sys._is_gil_enabled()}')"
```

- **参数**：`-X gil=0`

**输出**：

```
解释器路径: E:\Python\py314t\python3.14t.exe
GIL 是否已禁用: True
```

为什么官方要分两个文件？这是为了**生产环境的安全**考虑。

- **标准版 (`python.exe`)**：性能经过数十年优化，最稳定，支持所有第三方库，但受 GIL 限制。
- **自由线程版 (`python3.14t.exe`)**：允许真正的多核并行，但因为内部加锁机制改变，单线程任务可能变慢，且部分旧的 C 扩展库可能会崩溃。

## 多线程性能检验

**代码：**

```python
import time
import threading
import sys

def cpu_task():
    count = 0
    for i in range(10_000_000):
        count += i

def run():
    print(
        f"Python版本: {sys.version.split()[0]} | GIL: {'禁用' if not sys._is_gil_enabled() else '开启'}"
    )

    # 1. 单线程顺序执行
    start = time.time()
    cpu_task()
    cpu_task()
    t_single = time.time() - start
    print(f"单线程（顺序执行2次）: {t_single:.4f} 秒")

    # 2. 多线程并行执行
    t1 = threading.Thread(target=cpu_task)
    t2 = threading.Thread(target=cpu_task)
    start = time.time()
    t1.start()
    t2.start()
    t1.join()
    t2.join()
    t_multi = time.time() - start
    print(f"多线程（并行执行2次）: {t_multi:.4f} 秒")

    print(f"加速比: {t_single / t_multi:.2f}x")

if __name__ == "__main__":
    run()
```

**输出**：

```
Python版本: 3.14.2 | GIL: 禁用
单线程（顺序执行2次）: 0.5047 秒
多线程（并行执行2次）: 0.2557 秒
加速比: 1.97x
```

