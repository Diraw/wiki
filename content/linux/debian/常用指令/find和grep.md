# find

## 使用

```bash
find . -name <target>
```

- `.`代表在当前目录下
- `-name`后面跟文件名字，可以加双引号也可以不加

<img src="https://img.diraw.top/i/2025/08/18/lzekcz.png"/>

- `-type`后面跟文件的类型
  - `f`：file 普通文件
  - `d`：directory 文件夹
  - `l`：symbolic link 符号链接（类似于win的快捷方式）
  - `s`：socket 套接字文件

## 模糊搜索

（1）`*`匹配任意字符

（2）`？`匹配单个字符

（3）`[]`匹配指定范围

```bash
find . -name "[abc]*"     # 查找以 a/b/c 开头的文件
find . -name "file[0-9]"  # 查找 file0、file1...file9
```

（4）`-iname`不区分大小写

```bash
find . -iname "*.jpg"     # 查找 .jpg、.JPG、.Jpg 等
```

（5）支持正则表达式

具体查看 [[正则表达式]]

# grep