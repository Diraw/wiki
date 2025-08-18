# 导入

```python
import pandas as pd
```
# 读取文件

```python
df = pd.read_excel(file: str, header=None)
df = pd.read_csv(file: str, header=None)
```

`header=None` 表示没有行头

指定行头、列头：
- `header=0, index_col=0`（`0` 是索引值）

自定义：
- 行头：`df.columns = header: list`
- 列头：`df.index = index: list`
# 行索引

**一个参数时**：
- 选择某一行：`df.loc[label: str/int]`（`label` 的类型虚与 `index` 的类型一致）
- 选择多行（指定名称）：`df.loc[list: list[label]]`
- 选择多行（切片）：`df.loc[label_1:label_2]`
- 布尔索引：`df.loc[df[label] > 25]`（选择 `df` 中 `label` 列 `>25` 的的所有行）

**两个参数时**：
- 选择单个元素：`df.loc[label_1, label_2]`
- 选择某一列：`df.loc[:, label]`
- 选择多列：`df.loc[:, list: list]`
- 选择行列的组合：`df.loc[list_1: list, list_2: list]`

**注**：`df.loc` 的切片是**包含**结束位置的，这与 Python 的列表切片（不包含结束位置）不同
# 位置索引

`df.iloc[row_position, column_position]`

和 `.loc[]` 一样功能，不同的是 `.loc[]` 的参数是 `label`，`.iloc[]` 的参数是数字：
- 单个整数、整数列表、整数切片（不包含结束位置）

# 选择列

直接使用 `df[label]` 或 `df[[label1, label2]]`  来选择列，这是更常见的单列/多列选择方式

# 保存文件

**保存为 CSV 文件**：

`df.to_csv(path_or_buffer, header=True, index=True)`
- `index` 和 `header` 默认是保存的，一般用 `False` 取消

路径问题：

```python
import os
os.makedirs(path, exist_ok=True)
df.to_csv(f"{path}/{file_name: str}", index=False, header=False)
```

**保存为 Excel 文件**：

`df.to_excel(path/excel_writer, sheet_name='Sheet1', header=False, index=False, na_rep='')`
- `sheet_name` 默认为 `'Sheet1'`
- `na_rep`: 用于表示缺失值（`NaN`）的字符串，默认为空字符串

保存一个工作表的话，用 `path: str` 就行；需要保存到不同工作表，用 `excel_writer`：

```python
with pd.ExcelWriter(f"{path}/{file_name: str}") as w:
    df.to_excel(w, sheet_name: str)
    df.to_excel(w, sheet_name: str)
```

# 添加数据

**从左往右**：

- 一列一列添加
```python
empty_df = pd.DataFrame(index: list[str/int])
empty_df['ColumnA'] = data_col_a: list
```
- 收集好最后创建（**推荐**）
```python
data_dict = {}
my_dict[label_1: str, int] = list_1
my_dict[label_2: str, int] = list_2

df_from_dict = pd.DataFrame(data_dict, index: list
```
- `index` 的个数需要和列的行数一致

**从上往下**：

- 一行一行添加
```python
df.loc[header] = row_data
```
- 收集好最后创建（**推荐**）
```python
rows_data = []
all_rows_data.append(row_dict: dict)
df = pd.DataFrame(all_rows_data)
```
- `row_dict` 的 `key` 是列名，`value` 是值

也就是说，`pd.DataFrame` 的输入是 `dict` 就是按列创建，是 `list` 就是按行创建

**函数式添加**：

使用 `pd.concat()` 将新的 `new_df` 加到原来的 `df` 上：
- **按行添加 (`axis=0`)**：
```python
new_row_df = pd.DataFrame([row_dict])
df = pd.concat([df, new_row_df], ignore_index=True)
```
- 添加 `ignore_index=True` 会重置两个 `df` 的 `index` 列名，不添加则会保留
- **按列添加 (`axis=1`)**：
```python
new_column_df = pd.DataFrame({'NewColumn': list_data}, index=df.index)
df = pd.concat([df, new_column_df], axis=1)
```

**Pandas 的设计理念**：

1. **行索引 (index)**：通常被视为记录的唯一标识符。在按行连接时，如果原始索引可能重复或不连续，我们经常需要一个新的、干净的索引。这就是 `ignore_index=True` 的作用。
2. **列索引 (columns)**：通常被视为数据的属性或特征名称。在按列连接时，我们通常希望保留这些有意义的名称，或者明确地指定新列的名称。
# 运算操作

提取列，对应元素操作：
- 相加：`df['col1'] + df['col2']`，`df[["col1", "col2"]] + df[["col1", "col2"]]`
- 数字：`df['col1'] + 1`
- 除了 `+`，所有基本的算术运算符 (`-`, `*`, `/`, `//`, `%`, `**`) 都可以这样使用

对全局、对行（`.loc`）操作一样（广播机制）