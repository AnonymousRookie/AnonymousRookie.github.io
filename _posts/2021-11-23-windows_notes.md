---
layout: post
title: "Windows学习笔记"
categories: Windows
tags: Windows
author: ZhangJie
---

* content
{:toc}
Windows学习笔记。




## 基本命令

```
// 查找占用某个Port的进程
netstat -ano | find "port_id"

// 根据进程ID查找该进程的名称
tasklist | findstr "process_id"

// 查看本机的路由表
Route Print

// 打开服务
services.msc

// 打开画板
mspaint

// 关闭进程
taskkill /f /im iexplore.exe 

// 计算器
calc

// 记事本
notepad

// 远程桌面连接
mstsc
```


## Windows系统编程

### 尽量使用_beginthreadex()来代替使用CreateThread()

_beginthreadex()比较于 CreateThread()有更高的线程安全性，不会造成多个线程共用同一个全局变量的情况。

首先要从标准C运行库与多线程的矛盾说起，标准C运行库在1970年被实现了，由于当时没任何一个操作系统提供对多线程的支持。因此编写标准C运行库的程序员根本没考虑多线程程序使用标准C运行库的情况。比如标准C运行库的全局变量errno。很多运行库中的函数在出错时会将错误代号赋值给这个全局变量，这样可以方便调试。但如果有这样的一个代码片段：


```c
 if (system("notepad.exe readme.txt") == -1)  
{  
    switch(errno)  
    {  
        ...//错误处理代码  
    }  
}  
```

假设某个线程A在执行上面的代码，该线程在调用system()之后且尚未调用switch()语句时另外一个线程B启动了，这个线程B也调用了标准C运行库的函数，不幸的是这个函数执行出错了并将错误代号写入全局变量errno中。这样线程A一旦开始执行switch()语句时，它将访问一个被B线程改动了的errno。这种情况必须要加以避免！因为不单单是这一个变量会出问题，其它像strerror()、strtok()、tmpnam()、gmtime()、asctime()等函数也会遇到这种由多个线程访问修改导致的数据覆盖问题。
 
为了解决这个问题，Windows操作系统提供了这样的一种解决方案——每个线程都将拥有自己专用的一块内存区域来供标准C运行库中所有有需要的函数使用。而且这块内存区域的创建就是由C/C++运行库函数_beginthreadex()来负责的。 
 
_beginthreadex()最终还是会调用CreateThread()来向系统申请创建线程。
 
_beginthreadex()函数在创建新线程时会分配并初始化一个_tiddata块。这个_tiddata块自然是用来存放一些需要线程独享的数据。事实上新线程运行时会首先将_tiddata块与自己进一步关联起来。然后新线程调用标准C运行库函数如strtok()时就会先取得_tiddata块的地址再将需要保护的数据存入_tiddata块中。这样每个线程就只会访问和修改自己的数据而不会去篡改其它线程的数据了。因此，如果在代码中有使用标准C运行库中的函数时，尽量使用_beginthreadex()来代替CreateThread()。 
 





