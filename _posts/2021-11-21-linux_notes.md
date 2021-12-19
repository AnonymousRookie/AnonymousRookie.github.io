---
layout: post
title: "Linux学习笔记"
categories: Linux
tags: Linux
author: ZhangJie
---

* content
{:toc}
Linux学习笔记。




## 基本命令

- 文件内容查阅

```
// cat: 由第一行开始显示文件内容
// nl: 显示的时候，顺便输出行号
// head: 只看头几行
// tail: 只看结尾几行

/*示例*/
$ nl helloworld.go 
     1  package main
       
     2  import (
     3    "fmt"
     4  )
       
     5  func main() {
     6    fmt.Println("hello world...")
     7  }
```

- 文件查找

```
whereis [参数] 文件或目录名

/*示例*/
$ whereis java
java: /opt/jdk1.7.0_80/bin/java /opt/jdk1.7.0_80/jre/bin/java
```

```
find 目录 -name 文件名

/*示例*/
$ find ./ -name wechat.go
./wechat_go/wechat.go
./wechat_go/src/github.com/wechat.go
```


- 查找文件夹下包含某个关键字的所有文件

```
$ ls ./fff
a.txt  b.txt  c.txt
$ grep -r "hello" ./fff
./fff/c.txt:cccc hello world
./fff/a.txt:hello abc
```


- 文件权限操作

```
u 表示“用户（user）”，即文件或者目录所有者
g 表示“同组（group）用户”，即文件属主有相同组ID的所有用户
o 表示“其他（others）用户”，即系统默认值

文字设定：（r：可读）、（w：可写）、（x：可执行）
数字设定：（r：4）、（w：2）、（x：1）
```

```
chmod ugo+rwx a.txt: 表示分别给a.txt文件g、u、o用户可读、可写、可执行权限

chmod 777 a.txt: 表示分别a.txt文件g、u、o用户可读、可写、可执行权限

chmod u-wx a.txt：表示删除a.txt文件u用户可写、可执行权限

/*示例*/
$ ll a.txt 
-rw-rw-r-- 1 jason jason 0 Feb  1 16:14 a.txt
$ chmod ugo+rwx a.txt
$ ll a.txt 
-rwxrwxrwx 1 jason jason 0 Feb  1 16:14 a.txt
```


- 打包和压缩、解压缩文件

```
bunzip2 file1.bz2: 解压一个叫做 'file1.bz2' 的文件 
bzip2 file1: 压缩一个叫做 'file1' 的文件 

gunzip file1.gz: 解压一个叫做 'file1.gz' 的文件 
gzip file1: 压缩一个叫做 'file1' 的文件 
gzip -9 file1: 最大程度压缩 

rar a file1.rar test_file: 创建一个叫做 'file1.rar' 的包 
rar a file1.rar file1 file2 dir1: 同时压缩 'file1', 'file2' 以及目录 'dir1' 
unrar x file1.rar: 解压rar包 

tar -cvf archive.tar file1 file2 dir1: 创建一个包含了'file1', 'file2' 以及'dir1'的压缩文件 
tar -tf archive.tar: 显示一个包中的内容 
tar -xvf archive.tar: 释放一个包 
tar -xvf archive.tar -C /tmp: 将压缩包释放到/tmp目录下 
tar -cvfj archive.tar.bz2 dir1: 创建一个bzip2格式的压缩包 
tar -jxvf archive.tar.bz2: 解压一个bzip2格式的压缩包 
tar -cvfz archive.tar.gz dir1: 创建一个gzip格式的压缩包 
tar -zxvf archive.tar.gz: 解压一个gzip格式的压缩包 

zip file1.zip file1: 创建一个zip格式的压缩包 
zip -r file1.zip file1 file2 dir1 将几个文件和目录同时压缩成一个zip格式的压缩包 
unzip file1.zip: 解压一个zip格式压缩包 
```


- 后台运行app

```
./app &
```

- 查看当前某个进程信息

```
ps -aux | grep "sshd"
```


## 常见错误及解决方法

- shell脚本报错：No such file or directory #!/bin/bash

主要原因是该shell脚本是在windows下编辑然后上传到linux系统里执行的。
该.sh文件的格式为dos格式，而linux只能执行格式为unix格式的脚本。

可以通过vi编辑器来查看文件的format格式。

在vi命令模式中使用 :set ff 命令，可以看到该文件的格式为fileformat=dos。

修改文件格式：

修改文件format为unix

使用vi修改文件format

```
:set ff=unix
```

执行完后再通过set ff命令查看文件格式。



- error: C++ preprocessor "/lib/cpp" fails sanity check 问题的解决


问题的根源是缺少必要的C++库。

如果是CentOS系统，运行如下命令解决：

```
yum install glibc-headers
yum install gcc-c++ 
```

Ubuntu系统中，运行命令：

```
apt-get install build-essential 
apt-get install g++
```


- /bin/ld: cannot find -lprotobuf-lite

```
libprotobuf-lite.a

加载a库，即用gcc命令生成目标文件时指明静态库名，gcc将会从静态库中将公用函数连接到目标文件中。注意，gcc会在静态库名前加上前缀lib，然后追加扩展名.a得到的静态库文件名来查找静态库文件。

-lprotobuf-lite <==> libprotobuf-lite.a

```

- gcc C++11 thread

```
terminate called after throwing an instance of 'std::system_error'
  what():  Enable multithreading to use std::thread: Operation not permitted
Aborted
```

```
// CMakeLists.txt

set(CMAKE_CXX_FLAGS "-std=c++11 -pthread")
```



## Linux系统编程

### gcc

#### gcc的工作流程

- 预处理：cpp预处理器，去掉注释，展开头文件，宏替换
```
gcc -E test.c -o test.i
```

- 编译：gcc，将源代码文件编译成汇编语言代码
```
gcc -S test.i -o test.s
```

- 汇编：as，将汇编语言代码编译成二进制文件（目标代码）
```
gcc -C test.s -o test.o
```

- 链接：ld，链接test.c代码中调用的函数库
```
gcc -o test test.o
```


#### 静态库的制作

- 将.c文件编译成.o文件
```
gcc -c fun1.c fun2.c
```

- 使用ar命令将.o文件打包成.a文件
```
ar rcs libtest.a fun1.o fun2.o
```

#### 静态库的使用

- main.c与head.h和libtest.a在同一级目录的情况
```
gcc -o main main.c -I./ -L./ -ltest
```

- main.c与head.h和libtest.a不在同一级目录的情况
```
gcc -o main main.c -I./include -L./lib -ltest
```

#### 动态库的制作

- 将.c文件编译成.o文件
```
gcc -fpic -c fun1.c fun2.c
```

- 使用gcc将.o文件编译成库文件
```
gcc -shared fun1.o fun2.o -o libtest.so
```

#### 动态库的使用

```
gcc -o main main.c -I./include -L./lib -ltest
```




