layout: post
title: Shell学习笔记(一)
comment: true
date: 2016-05-01 10:21:56
tags: [Shell,技术,笔记] 
---

# 基本的bash shell命令

## 常见Linux目录名称

| 目录     | 用途                      |
| ------ | ----------------------- |
| /      | 虚拟目录的根目录                |
| /bin   | 二进制目录，存放许多GUN用户级的工具     |
| /boot  | 启动目录，存放启动文件             |
| /dev   | 设备目录，Linux在这里创建用户目录     |
| /etc   | 系统配置文件目录                |
| /home  | 主目录，Linux在这里创建用户目录      |
| /lib   | 库目录，存放系统和应用程序的库文件       |
| /media | 媒体目录，存放可移动媒体设备挂载点的地方    |
| /mnt   | 挂载目录，另一个存放可移动设备挂载点的地方   |
| /opt   | 可选目录，用于存放可选的软件包         |
| /root  | 根主目录                    |
| /sbin  | 系统二进制目录，存放许多GUN管理员级工具   |
| /tmp   | 临时目录，可以在该目录中创建和删除临时文件   |
| /usr   | 用户安装软件的目录               |
| /var   | 可变目录，用以存放经常变化的文件，比如日志文件 |

<!-- more -->

## 处理文件

### 创建文件

使用touch命令创建空文件

```shell
$ touch test1
```

使用touch改变已有文件的访问时间和修改时间。-a参数只改变访问时间，-t参数只改变修改时间

```shell
$ touch test1
$ touch -t 201112251200 test1
```

### 复制文件

```shell
$ cp source destination
```

### 链接文件

Linux中有两种不同类型的文件链接：符号链接（软链接）和硬链接。

软连接实际上是一个文本，文本中会记录源文件的位置信息。

硬链接会创建一个独立文件，其中包含源文件的信息及位置。引用硬链接文件等同于引用源文件。

### 重命名文件

移动文件会改变文件名，但保持同样的索引节点号和时间戳。

```shell
$ mv source detination
```

### 删除文件

可以使用rm命令移除文件，使用-f参数来强制删除。

```shell
$ rm test1
```

## 处理目录

### 创建和删除目录

使用mkdir命令创建目录。

```shell
$ mkdir dir1
```

默认情况下，rmdir命令只删除空目录。可以使用—ignore-fail-on-non-empty参数来删除非空目录

```shell
$ rmdir dir1
```

使用rm命令和-r参数递归删除目录中的文件，最后删除目录自身。同样可以使用-f参数强制删除。

```shell
$ rm -rf dir1
```

### 查看文件内容

`stat`查看文件的所有状态信息

`file`查看文件类型

`cat`显示文本文件中所有数据

`more`逐页显示文本文件的内容

`tail`和`head`显示尾部和头部的部分文件

# 更多bash shell命令

## 监测程序

ps命令显示进程号、运行在哪个终端、进程已用的CPU时间

top命令实时显示进程信息

kill命令可以通过PID（进程号）结束进程

kill命令通过进程名来结束进程

## 监测磁盘空间

mount命令显示当前系统上挂载的设备列表

手动挂载媒体设备的基本命令如下,type参数指定了磁盘被格式化的文件类型。

```shell
$ mount -t type device directory
```

使用umount命令通过设备文件或者挂载点来指定要卸载的设备。

```shell
$ umount [directory | device]
```

使用df命令查看已挂载磁盘的使用情况，du命令显示某个特定目录（默认是当前目录）的磁盘使用情况。

##  处理数据文件

使用sort命令对文本文件中的数据进行排序。-M参数用三字符月份名按月份排序，-n参数按字符串数值来排序。

```shell
$ sort file1
```

使用grep命令查找大文件中的一行数据。-v参数反向搜索（输出不匹配该模式的行），-n参数显示匹配模式的行所在的行号，-c参数显示有多少行含有匹配的模式，-e参数指定多于一个匹配模式

```shell
$ grep [options] pattern [file]
$ grep -n t file1
$ grep -e t -e f file1
```

使用压缩工具对数据进行压缩，Linux上部分的文件压缩工具如下：

| 工具       | 文件扩展名 | 描述                               |
| -------- | ----- | -------------------------------- |
| bzip2    | .bz2  | 采用Burrows-Wheeler块排序文本压缩算法和霍夫曼编码 |
| compress | .Z    | 原始的Unix文件压缩工具，逐渐消失中              |
| gzip     | .gz   | GNU压缩工具，用Lempel-Ziv编码            |
| zip      | .zip  | Windows上PKZIP工具的Unix实现           |

使用tar命令对数据进行归档

```shell
$ tar [options] object1 object2
$ tar -cvf test.tar test/ test2/
$ tar -xvf test.tar
```

