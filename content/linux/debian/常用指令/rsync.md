# 简介

`rsync` 是一个高效的文件同步工具，可以通过 SSH 传输文件。它的特点是：

- **增量传输**：只传输变化的部分
- **支持断点续传**
- **可以保持文件权限、时间戳等信息**

在windows上推荐使用git或者wsl进行文件同步

# 格式

假设：

- 服务器 IP：`192.168.1.100`
- 服务器用户名：`user`
- 要下载的目录：`/home/user/data`
- 本地保存目录：`C:\Users\YourName\Desktop\data`

运行：

```bash
rsync -avz user@192.168.1.100:/home/user/data/ /c/Users/YourName/Desktop/data/
```

参数解释：

- `-a`：归档模式（保留权限、时间戳等）
- `-v`：详细输出（verbose）
- `-z`：压缩传输（加快速度）
- `user@192.168.1.100`：SSH 登录信息
- `/home/user/data/`：服务器上的路径（最后的 `/` 表示目录内容）
- `/c/Users/...`：Windows 本地路径

其他选项：

- `--progress`：显示传输进度
- `--delete`：删除目标目录中多余的文件（同步时用）
- `-P`：断点续传

# 使用

## 上传文件

把本地 `file.txt` 上传到服务器 `/home/user/` 目录：

```bash
rsync -avz /c/Users/YourName/Desktop/file.txt user@192.168.1.100:/home/user/
```

## 下载文件

把服务器上的 `file.txt` 下载到本地桌面：

```bash
rsync -avz user@192.168.1.100:/home/user/file.txt /c/Users/YourName/Desktop/
```

## 上传目录

上传 `data` 目录的内容到服务器：

注意末尾的 `/` 表示只上传目录里的内容，而不是目录本身。

```bash
rsync -avz /c/Users/YourName/Desktop/data/ user@192.168.1.100:/home/user/data/
```

## 下载目录

下载服务器 `data` 目录内容到本地：

```bash
rsync -avz user@192.168.1.100:/home/user/data/ /c/Users/YourName/Desktop/data/
```

## 双向同步

让服务器目录与本地保持一致（并删除服务器多余文件）：

```bash
rsync -avz --delete /c/Users/YourName/Desktop/data/ user@192.168.1.100:/home/user/data/
```

反方向同步（服务器下载到本地）：

```bash
rsync -avz --delete user@192.168.1.100:/home/user/data/ /c/Users/YourName/Desktop/data/
```

