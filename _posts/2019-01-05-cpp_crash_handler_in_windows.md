---
layout: post
title:  "C++程序Crash时记录CallStack信息并生成Dump文件(Windows)"
categories: C++ 工具
tags: C++ 工具 CallStack Dump
author: ZhangJie
---

* content
{:toc}
在平时的程序开发中，我们一般都会在程序的关键部位记录相应的日志信息，方便以后排查bug，但是当程序Crash的时候，通过有限的日志有时很难定位问题所在。因此，我们需要在程序Crash时能够自动记录导致Crash的CallStack信息并且生成Dump文件。Google Breakpad功能十分强大，能够很好地满足我们的需求。但有时如果没有特殊的需求的话，为了使用起来既简单又方便，我们完全可以自己实现，本文将对此进行简要介绍。





完整程序及测试程序详见：[https://github.com/AnonymousRookie/useful-tools/tree/master/crash_handler/Windows](https://github.com/AnonymousRookie/useful-tools/tree/master/crash_handler/Windows)

我们只需要在自己的程序中包含crash_handler.h，并且在main函数的开始处添加：
```c
z::RunCrashHandler();
```

程序运行如果出现Crash，则会生成2个文件2019_1_5_20_1_23_callstack.log和2019_1_5_20_1_23_crash.dmp。

#### 2019_1_5_20_1_23_callstack.log中记录导致Crash的CallStack信息：
```
ErrorCode: C0000094
Address: 00353785
Flags: 00000000
CallStack:
main() : [crash_handler_test] (File: c:\users\62116\documents\visual studio 2010\projects\crash_handler_test\crash_handler_test\test.cpp @Line 11)
__tmainCRTStartup() : [crash_handler_test] (File: f:\dd\vctools\crt_bld\self_x86\crt\src\crtexe.c @Line 555)
mainCRTStartup() : [crash_handler_test] (File: f:\dd\vctools\crt_bld\self_x86\crt\src\crtexe.c @Line 371)
BaseThreadInitThunk() : [kernel32] (File: N/A @Line N/A)
RtlInitializeExceptionChain() : [ntdll] (File: N/A @Line N/A)
RtlInitializeExceptionChain() : [ntdll] (File: N/A @Line N/A)
```

通过上述CallStack信息基本上都能够定位到问题所在。

#### 下面简单介绍下dump文件的用法：

将dump文件copy到含有应用程序exe以及pdb文件的目录，通过VS打开，直接调试运行，代码就会自动停到导致crash的那一行。
