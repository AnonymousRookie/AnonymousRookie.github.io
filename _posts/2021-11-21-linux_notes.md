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

```go
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

```sh
whereis [参数] 文件或目录名

/*示例*/
$ whereis java
java: /opt/jdk1.7.0_80/bin/java /opt/jdk1.7.0_80/jre/bin/java
```

```sh
find 目录 -name 文件名

/*示例*/
$ find ./ -name wechat.go
./wechat_go/wechat.go
./wechat_go/src/github.com/wechat.go
```


- find 用法

https://blog.csdn.net/qq_42716761/article/details/128573559

查找某个目录中指定文件大小范围的文件。

```sh
find ./tmp -size 0 -type f 
```

- 查找文件夹下包含某个关键字的所有文件

```sh
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

```sh
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

```sh
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

```sh
// 压缩.gz
gzip –c filename > filename.gz // 压缩同时保留源文件

// 解压.tar.gz
tar -xzvf file.tar.gz //解压tar.gz

// 解压.tar.bz2
tar -jxvf file.tar.bz2

// 解压.tar.xz
tar -xvJf file.tar.xz

// 加密压缩zip
sudo zip -re file.zip file     
Enter password: ***
Verify password: ***
```


- 后台运行app

```sh
./app &
```

- 查看当前某个进程信息

```sh
ps -aux | grep "sshd"
```


- 查看Linux内核版本
```sh
// uname -a 
jason@jason-virtual-machine:~/github$ uname -a
Linux jason-virtual-machine 3.13.0-24-generic #46-Ubuntu SMP Thu Apr 10 19:08:14 UTC 2014 i686 i686 i686 GNU/Linux
// cat /proc/version
jason@jason-virtual-machine:~/github$ cat /proc/version
Linux version 3.13.0-24-generic (buildd@roseapple) (gcc version 4.8.2 (Ubuntu 4.8.2-19ubuntu1) ) #46-Ubuntu SMP Thu Apr 10 19:08:14 UTC 2014
```

- Linux下载命令
```sh
wget https://sourceforge.net/projects/boost/files/boost/1.58.0/boost_1_58_0.tar.gz
```


- 复制、删除与移动: cp, rm, mv

```
复制cp
cp [参数] 源文件 目标文件

常用参数: 
-a：使得 目标文件 与 源文件 具有完全相同的属性
-i: 若 目标文件 已经存在，覆盖时会提示是否继续
-r: 递归复制，用于复制 目录
-p: 复制时保留文件属性
```

```
删除rm
rm [参数] 文件或目录

常用参数: 
-f: 强制删除，不提示用户，直接删除
-i: 删除前询问是否继续
-r: 递归删除，最常用在目录删除（需谨慎）
```

```
移动mv
移动 文件 或 目录，或重命名
mv [参数] 源文件或目录 目标文件或目录

常用参数: 
-f: 强制执行，不提示用户
-i: 执行前询问是否继续
```


- 查看文件的行数
```sh
wc -l 文件名
```

- 新建用户
```sh
useradd -d /home/jason -m jason
passwd jason
```

- tree
```sh
tree -d -N folder
-d: 只显示目录
-N: 中文正常显示
```

- 查看内存使用情况
```sh
free: 是一个快速查看内存使用情况的方法;
top: 是Linux下常用的性能分析工具，能够实时显示系统中各个进程的资源占用状况，类似于Windows的任务管理器。
```


- 关闭某个进程

```sh
kill -9 PID  (PID：进程号)
```


- 查看硬盘使用情况
```sh
df -hl: 查看磁盘剩余空间
```



- 根据错误码查看错误信息
```sh
perror 98
==> OS error code  98:  Address already in use
```


- ipcs
```sh
$ ipcs -l

---------- 同享内存限制 ------------
max number of segments = 4096
max seg size (kbytes) = 32768
max total shared memory (kbytes) = 8388608
min seg size (bytes) = 1

--------- 信号量限制 -----------
最大数组数量 = 128
每个数组的最大信号量数目 = 250
系统最大信号量数 = 32000
每次信号量调用最大操作数 = 32
信号量最大值 = 32767

------ Messages Limits --------
系统最大队列数量 = 1657
max size of message (bytes) = 8192
default max size of queue (bytes) = 16384
```

- ldconfig

```
ldconfig是一个动态链接库管理命令，其目的为了让动态链接库为系统所共享。
```

- 目录的相关操作

```
.  代表此层目录
.. 代表上一级目录
-  代表前一个工作目录
~  代表"当前用户身份"所在的主文件夹
```

```
cd: 切换目录

cd /home: 进入/home目录
cd ..: 返回上一级目录
cd ../..: 返回上两级目录
cd ~: 进入个人的主目录
cd -: 返回上次所在的目录
```

```
pwd: 显示当前目录(print working directory)
```


```
mkdir: 新建一个新的目录

mkdir dir1: 创建一个的目录dir1
mkdir dir1 dir2: 同时创建两个目录dir1和dir2
mkdir -p /tmp/dir1/dir2: 创建一个目录树

rmdir: 删除一个空的目录

rmdir dir1: 删除目录dir1
```

- 显示网络设备信息

```
ifconfig
```

- 完整的切换到一个用户环境（相当于登录）

```sh
su - 用户名: 切换到新的用户
exit: 退出

/*示例*/
[jason@izbp170tm6dyv1or9j6xgyz ~]$ su - git
Password: 
Last login: Sat Feb  1 15:49:21 CST 2020 from 112.20.1.250 on pts/8
[git@izbp170tm6dyv1or9j6xgyz ~]$ 
[git@izbp170tm6dyv1or9j6xgyz ~]$ exit
logout
[jason@izbp170tm6dyv1or9j6xgyz ~]$ 
```

- 关机、重启

```sh
shutdown -h now: 关机
shutdown -r now: 重启
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


- Unix error值

每当在一个Unix函数（如socket函数）中发生错误时，全局变量errno被置成一个指示错误类型的正整数。

```sh
$ perror 0
OS error code   0:  Success
$ perror 1
OS error code   1:  Operation not permitted
$ perror 2
OS error code   2:  No such file or directory
$ perror 3
OS error code   3:  No such process
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

```sh
gcc -o main main.c -I./include -L./lib -ltest
```

## Linux目录结构

- /usr：系统级的目录，可以理解为C:/Windows/；
- /usr/lib：理解为C:/Windows/System32；
- /usr/local：用户级的程序目录，可以理解为C:/Progrem Files/，用户自己编译的软件默认会安装到这个目录下；
- /opt：用户级的程序目录，可以理解为D:/Software，opt有可选的意思，这里可以用于放置第三方大型软件（或游戏），当你不需要时，直接rm -rf掉即可，在硬盘容量不够时，也可将/opt单独挂载到其他磁盘上使用；
- /usr/src：系统级的源码目录；
- /usr/local/src：用户级的源码目录。





