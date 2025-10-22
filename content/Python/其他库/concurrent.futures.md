`concurrent.futures` 模块是 Python 标准库的一部分，它提供了一种高级接口来异步执行可调用对象。它通过使用线程池或进程池来管理并发任务，从而简化了并发编程。

# 核心概念

`concurrent.futures` 主要围绕两个核心概念：

1. **`Executor`**: 这是一个抽象基类，定义了提交可调用对象以供异步执行的接口。它有两个具体的实现：
    
    - `ThreadPoolExecutor`: 使用**线程池**来执行任务。**适用于 I/O 密集型任务**，因为线程在等待 I/O 时可以释放 GIL（全局解释器锁），从而允许其他线程运行。
    - `ProcessPoolExecutor`: 使用**进程池**来执行任务。**适用于 CPU 密集型任务**，因为它绕过了 GIL 的限制，允许真正的并行计算。
2. **`Future`**: 这是一个表示异步操作结果的对象。当你向 `Executor` 提交一个任务时，它会立即返回一个 `Future` 对象。你可以使用 `Future` 对象来检查任务的状态、获取任务的结果或捕获任务中发生的异常。

# 基本用法

## 导入模块：

```python
import concurrent.futures
```

## 创建 Executor：

```python
with concurrent.futures.ThreadPoolExecutor(max_workers=3) as executor:
	# ...
```

```python
with concurrent.futures.ProcessPoolExecutor(max_workers=3) as executor:
	# ...
```

- `max_workers` 参数指定了池中进程的最大数量。可以用 `max_workers=os.cpu_count()` 计算。

## 提交任务：

- **`submit(fn, *args, **kwargs)`**：提交一个可调用对象 `fn` 及其参数。它会立即返回一个 `Future` 对象。

```python
future1 = executor.submit(task, "A")
future2 = executor.submit(task, "B")
```

- **`map(fn, *iterables)`**: 提交一个可调用对象 `fn`，并将其应用于 `iterables` 中的每个元素。它返回一个迭代器，该迭代器会按提交顺序产生结果。

```python
results_iterator = executor.map(task, ["C", "D", "E"])
```

## 处理 `Future` 对象：

- **`result(timeout=None)`**: 阻塞直到任务完成并返回其结果。如果任务引发异常，则此方法会重新引发该异常。`timeout` 参数可以指定等待结果的最大时间。

```python
print(f"Result from Future 1: {future1.result()}")
print(f"Result from Future 2: {future2.result()}")
```

- **`exception(timeout=None)`**: 阻塞直到任务完成并返回其引发的异常。如果任务成功完成，则返回 `None`。

```python
# 假设 task_with_error 会引发异常
future_error = executor.submit(task_with_error)
if future_error.exception():
    print(f"Error: {future_error.exception()}")
```

- **`done()`**: 返回 `True` 如果任务已完成或被取消，否则返回 `False`。

```python
if future1.done():
print("Future 1 is done.")
```

- **`running()`**: 返回 `True` 如果任务正在运行，否则返回 `False`。

- **`cancelled()`**: 返回 `True` 如果任务已取消，否则返回 `False`。

- **`add_done_callback(fn)`**: 当任务完成时，调用 `fn`。`fn` 接受一个 `Future` 对象作为参数。

```python
def done_callback(future):
	print(f"Callback: Task {future.result()} finished.")

future3 = executor.submit(task, "F")
future3.add_done_callback(done_callback)
```

## 使用 `concurrent.futures.as_completed()`：

这个函数接受一个 `Future` 对象的可迭代对象，并返回一个迭代器，该迭代器会在 `Future` 对象完成时按完成顺序产生它们。

这在处理大量任务时非常有用，因为你不需要等待所有任务都完成才能开始处理结果。

```python
futures = [executor.submit(task, str(i)) for i in range(5)]
for future in concurrent.futures.as_completed(futures):
	print(f"Completed task result: {future.result()}")
```

# 何时选择 `ThreadPoolExecutor` 或 `ProcessPoolExecutor`

- **`ThreadPoolExecutor`**:
    
    - **I/O 密集型任务**: 当任务大部分时间都在等待外部资源（如**网络请求、文件读写、数据库查询**）时，线程是更好的选择。因为在等待 I/O 时，Python 的 GIL 会被释放，允许其他线程运行，从而提高整体吞吐量。
    - **共享内存**: 线程共享同一进程的内存空间，因此在需要频繁共享数据时，线程间的通信成本较低。
- **`ProcessPoolExecutor`**:
    
    - **CPU 密集型任务**: 当任务大部分时间都在进行计算（如**图像处理、科学计算、数据分析**）时，进程是更好的选择。每个进程都有自己的 Python 解释器和 GIL，因此可以实现真正的并行计算，充分利用多核 CPU。
    - **隔离性**: 进程之间是相互隔离的，一个进程的崩溃不会影响其他进程。这对于需要更高稳定性的应用很有用。
    - **内存开销**: 进程的创建和通信开销通常比线程大，因为每个进程都需要独立的内存空间。

# 注意事项

- **GIL (Global Interpreter Lock)**: 这是 Python 中一个重要的概念。在任何给定时间，只有一个线程可以执行 Python 字节码。这意味着对于纯 CPU 密集型任务，`ThreadPoolExecutor` 无法实现真正的并行计算。`ProcessPoolExecutor` 通过使用独立的进程来绕过 GIL 的限制。
- **异常处理**: 务必在 `future.result()` 或迭代 `map` 结果时处理可能发生的异常，否则异常会在主线程中重新抛出。
- **资源管理**: 使用 `with` 语句来创建 `Executor` 是最佳实践，它能确保在任务完成后正确关闭池。
- **`shutdown()` 方法**: 如果不使用 `with` 语句，你需要在完成所有任务后手动调用 `executor.shutdown()` 来关闭执行器。

