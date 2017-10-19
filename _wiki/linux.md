---
layout: wiki
title: Linux/Unix
categories: Linux
description: 类 Unix 系统下的一些命令和用法。
keywords: Linux
---

类 Unix 系统下的一些常用命令和用法。

## 实用命令

### fuser

查看文件被谁占用。

```sh
fuser -u .linux.md.swp
```

### id

查看当前用户、组 id。

### lsof

查看打开的文件列表。

> An  open  file  may  be  a  regular  file,  a directory, a block special file, a character special file, an executing text reference, a library, a stream or a network file (Internet socket, NFS file or UNIX domain socket.)  A specific file or all the files in a file system may be selected by path.

#### 查看网络相关的文件占用

```sh
lsof -i
```

#### 查看端口占用

```sh
lsof -i tcp:5037
```

#### 查看某个文件被谁占用

```sh
lsof .linux.md.swp
```

#### 查看某个用户占用的文件信息

```sh
lsof -u mazhuang
```

`-u` 后面可以跟 uid 或 login name。

#### 查看某个程序占用的文件信息

```sh
lsof -c Vim
```

注意程序名区分大小写。

### 终端

如果进入的图形界面，那么tty1是图形界面。 tty2-6是字符界面

```
图形界面切换到字符界面：Ctrl+Alt+F2-6
字符界面切换到其他：Alt+F1-6
```

虚拟终端pts即为平时所用的终端。

### rpm

```
-i install
-q 查询
-v 提供更多信息输出
-e 删除
-h 输出hash
--nodeps 忽略依赖
```

### sed

```
sed -- stream editor
sed是一行一行处理文件内容，正在处理的放在缓冲区中。用来自动编辑一个或多个文件，简化对文件的反复操作，非交互编译文件。

-n 抑制自动输出 读取下一个输入行
-e 执行多个sed指令
-f 运行脚本
-i 编辑文件内容
a 在匹配后追加
i 在匹配后插入
p 打印
d 删除
r/R 读取文件/一行
w 另存
s 查找
c 替换
h/H 复制拷贝/追加缓冲区到存放空间
g/G 粘贴/从存放空间取回/追加到缓冲区
x 缓冲区内容交换
```

### awk

```
awk是优良的文本处理工具，是处理文本的编程语言工具。由模式和动作组成，一个awk脚本可以有多个语句，模式决定动作语句的触发条件和触发时间

特殊字段：
BEGIN 设置计数和打印头部信息，任何动作之前
END 语句输出统计结果，完成动作后执行
分隔符默认是空格，用-F改变成逗号，-F:可以变为冒号

awk 'BEGIN {} { 语句 } END{}' 文件
```

### Tricks

```
who am i 查询当前终端的进程号pts

cd - 直接返回上次cd的目录
```