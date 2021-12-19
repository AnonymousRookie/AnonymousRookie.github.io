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
