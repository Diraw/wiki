# shell

Shell 是一个广泛的概念。它指的是一个命令行解释器，用户可以通过它与操作系统进行交互，执行命令、运行程序。不同的操作系统有不同的 Shell，例如在 Linux 和 macOS 中常见的 Bash、Zsh 等，而在 Windows 中，CMD 和 PowerShell 都是 Shell 的一种。

PowerShell 是微软开发的一种特定的 Shell。它是在 Windows 系统上引入的，旨在提供比传统的 CMD 更强大、更灵活的命令行环境，尤其是在系统管理和自动化方面。

# 更新

windows自带的powershell是5，现在最新版本是7

查看powshell版本

```powershell
Get-Host
```

<img src="https://img.diraw.top/i/2025/08/18/lokj5e.png"/>

更新：

```powershell
winget search Microsoft.PowerShell
winget install --id Microsoft.PowerShell --source winget
```

<img src="https://img.diraw.top/i/2025/08/18/lp9mul.png"/>

然后把新下载的powershell设置为默认启动项：

<img src="https://img.diraw.top/i/2025/08/18/lp9lpe.png"/>

<img src="https://img.diraw.top/i/2025/08/18/lp9k4m.png"/>

<img src="https://img.diraw.top/i/2025/08/18/lp9qvb.png"/>

# cmdlet

Cmdlet（读作 command-let）是 PowerShell 中的基本命令单元。它们是轻量级的命令，用于执行特定的任务。与传统的命令行工具（如 Windows 命令提示符中的 dir 或 Linux 中的 ls）不同，Cmdlet 具有更强的结构化和一致性。

Cmdlet 的命名遵循“动词-名词”的结构，例如 Get-Process、Set-Location、New-Item。这种结构使得 Cmdlet 的功能更易于理解和记忆。动词表示要执行的操作（如 Get、Set、New），名词表示操作的对象（如 Process、Location、Item）。

# 使用

- Get-ChildItem (别名 ls, dir)：查看文件和目录。这是最常用的命令之一，用于查看当前目录或指定路径下的内容。
- Set-Location (别名 cd, chdir)：改变当前工作目录。频繁用于在不同目录之间切换。
- New-Item：创建文件或目录。创建新的脚本文件、配置文件或文件夹时常用。
- Remove-Item (别名 del, rm)：删除文件或目录。清理不再需要的文件和文件夹。
- Get-Process (别名 ps)：查看当前运行的进程。常用于查看哪些程序正在运行以及它们的资源占用情况。
- Clear-Host (别名 cls)：清空控制台屏幕。让你的终端界面更整洁。
- Invoke-WebRequest (别名 iwr, wget, curl)：下载网页内容或文件。在需要从网络获取数据时常用。
- Invoke-RestMethod：与 RESTful Web 服务交互。在自动化与 API 交互时常用。
- Get-Content (别名 cat, type)：读取文件内容。查看配置文件或日志文件时常用。

所以我觉得完全可以用linux的命令来弄，但有一些用不了（比如find、wget）

# 面向对象

powershell最大的特点是面向对象。bash和cmd传递的都是文本流，但是powershell传递的是对象。

举例：

在 Bash 中获取进程 ID 并杀死进程：

```bash
ps aux | grep "myprocess" | awk '{print $2}' | xargs kill
```

- ps aux 输出进程信息（文本），grep 过滤包含 "myprocess" 的行（文本），awk 解析文本提取进程 ID（文本），xargs kill 将进程 ID 作为参数传递给 kill 命令。整个过程涉及多次文本解析。

而PowerShell 的面向对象工作方式：

```powershell
Get-Process -Name myprocess | Stop-Process
```

- Get-Process -Name myprocess 返回一个或多个表示 "myprocess" 进程的 Process 对象。这些对象包含了进程的各种信息，如 ID、名称、CPU 使用率等。通过管道 |，这些 Process 对象直接传递给 Stop-Process 命令。Stop-Process 命令能够直接接收 Process 对象，并利用对象中的信息（如进程 ID）来停止进程，而无需进行文本解析。

# 查找

```powershell
Get-ChildItem -Path . -Filter "*.yml" -Recurse
```

- `Get-ChildItem`: PowerShell 中用于获取文件和目录的命令。
- `-Path .`: 指定搜索的路径为当前目录（. 表示当前目录）。
- `-Filter "*.yml"`: 使用过滤器来匹配文件名为 .yml 结尾的文件。请注意，在 PowerShell 中使用 -Filter 参数时，通配符（如 *）通常可以直接使用，不需要额外的引号，但为了保险起见，使用引号也是可以的。
- `-Recurse`: 递归地搜索子目录。

可简写为：

```powershell
Get-ChildItem -Path . -fi "*.yml" -r
```

或者用`-LiteralPath`的简写`-l`：

```powershell
Get-ChildItem -l . -fi "*.yml" -r
```

区别在于-Path支持通配符，-LiteralPath不支持通配符，但是我们这里用于过滤的是`-Filter`，所以并不受影响

**通配符**：

- `*`：匹配零个或多个任意字符。
  - 例如：`*.txt` 会匹配所有以 .txt 结尾的文件。
  - 例如：`data*` 会匹配所有以 data 开头的文件或目录。
- `?`：匹配一个任意字符。
  - 例如：`data?.txt` 会匹配 data1.txt、dataA.txt 等，但不匹配 data11.txt。
- `[]`：匹配方括号内列出的任意一个字符。
  - 例如：`[abc].txt` 会匹配 a.txt、b.txt 或 c.txt。
  - 例如：`[0-9].txt` 会匹配任何以单个数字开头，后跟 .txt 的文件。
- `[!...]` 或 `[^...]`：匹配不在方括号内列出的任意一个字符。
  - 例如：`[!abc].txt` 会匹配除了 a.txt、b.txt 或 c.txt 之外的任何以单个字符开头，后跟 .txt 的文件。
