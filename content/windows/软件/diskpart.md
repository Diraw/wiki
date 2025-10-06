DiskPart 是一个命令行磁盘分区工具，它允许用户管理计算机的磁盘、分区和卷

因为直接 右键开始菜单-磁盘管理 有时候会因为受保护等各种原因，无法直接识别或提供删除等选项，这时候就需要以管理员的身份启动 diskpart 了

# 常用命令

- `lisk disk`：显示计算机上所有可用的磁盘及其编号、大小和状态
- `select disk <disk_number>`：选择一个特定的磁盘进行操作
	- `<disk_number>` 是 `lisk disk` 命令显示出的磁盘编号
- `detail disk`：显示当前所选磁盘的详细信息，其中包括磁盘编号、型号、大小、状态以及分区信息
- `list partition`：显示当前所选磁盘上的所有分区
- `list partition <partition_number>`：选择一个特定的分区进行操作
	- `<partition_number>` 是 `list partition` 命令显示出的分区编号

- `active`：将当前所选的分区标记为活动分区，通常用于启动分区
- `inactive`：将当前所选的分区标记为非活动分区
- `clean [all]`：清除磁盘上的所有分区信息和数据
	- 不加 `all`：清除当前选定磁盘上的分区信息，但不会擦除磁盘上的所有数据，只是令所有分区变得不可访问，等待被新的数据覆盖，通常用于准备一个磁盘以重新分区
	- 加 `all`：彻底擦除磁盘上的每一个扇区，将所有数据归零，不可被恢复，通常用于在出售或丢弃硬盘之前彻底清除敏感数据
- `exit`：退出 DiskPart 命令行工具
# 创建分区

- `list partition primary [size=<size_in_mb>]`：在当前所选磁盘上创建一个主分区
	- `size` 参数是可选的，用于指定分区的大小（以 MB 为单位）
	- 如果不指定，则会使用所有可用空间
- `list partition extended [size=<size_in_mb>]`：在当前所选磁盘上创建一个扩展分区
	- 扩展分区可以包含多个逻辑驱动器
- `list partition logical [size=<size_in_mb>]`：在当前所选的扩展分区内创建一个逻辑驱动器
- `delete partition [override]`：删除当前所选的分区
	- `override` 参数用于强制删除受保护的分区（例如系统分区），但应谨慎使用

- `format fs=<filesystem> [label="<label_name>"] [quick]`：格式化当前所选的分区
	- `fs` 参数指定文件系统（例如 ntfs, fat32）
	- `label` 参数是可选的，用于指定卷标
	- `quick` 参数用于执行快速格式化
- `assign [letter=<drive_letter>]`：为当前所选的卷分配一个驱动器号
	- 如果未指定 `letter`，DiskPart 会自动分配一个可用的驱动器号。
- `remove [letter=<drive_letter>]`：移除当前所选卷的驱动器号

