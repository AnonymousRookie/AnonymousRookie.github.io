---
layout: post
title:  "基于SpringBoot+Mybatis的周报系统的实现"
categories: Java Web
tags: Web Java SpringBoot Mybatis bootstrap Thymeleaf
author: ZhangJie
---

* content
{:toc}
在公司里面，各个部门一般都会要求员工每周通过周报的形式对这一周的工作进行总结。本人所在的公司虽然有一个独立的部门专门负责公司的信息化建设，各大信息化系统总体来说还是挺全的，但是唯独没有一个周报管理系统，仍然采用Excel填写，邮件发送的形式，感觉既不方便填写，也不方便查看和统计。因此，前一段时间本人单独花了两周多时间开发了一个简单的周报系统。




平时工作主要以C++开发为主，但是想想周报系统最好还是做成web的形式，因此，打算利用java来进行开发，SSH框架太过于庞大，因此简单了解了一下目前主流的一些框架还有哪些，在网上搜索了一下，发现Spring Boot小巧简单，挺不错的。由于本人以前从来没有接触过Spring Boot，因此在github简单搜索了一下关键词Spring Boot，发现了很多基于Spring Boot开发的的后台管理系统，[bootdo](https://github.com/lcg0124/bootdo)看起来效果不错，因此打算基于bootdo进行周报系统的开发。




## 实现效果对比

### 开源的bootdo后台管理系统

- 登录页面

![https://AnonymousRookie.github.io/images/201809/20180922_01.png](https://AnonymousRookie.github.io/images/201809/20180922_01.png)

- 系统主页面

![https://AnonymousRookie.github.io/images/201809/20180922_02.png](https://AnonymousRookie.github.io/images/201809/20180922_02.png)

### 基于上述系统开发的周报系统

- 登录页面

![https://AnonymousRookie.github.io/images/201809/20180922_03.png](https://AnonymousRookie.github.io/images/201809/20180922_03.png)

- 系统主页面

![https://AnonymousRookie.github.io/images/201809/20180922_04.png](https://AnonymousRookie.github.io/images/201809/20180922_04.png)

- 周报填写页面

![https://AnonymousRookie.github.io/images/201809/20180922_05.png](https://AnonymousRookie.github.io/images/201809/20180922_05.png)

- 周报查询页面

![https://AnonymousRookie.github.io/images/201809/20180922_06.png](https://AnonymousRookie.github.io/images/201809/20180922_06.png)

- 周报统计页面

![https://AnonymousRookie.github.io/images/201809/20180922_07.png](https://AnonymousRookie.github.io/images/201809/20180922_07.png)

- 周报导出页面

![https://AnonymousRookie.github.io/images/201809/20180922_08.png](https://AnonymousRookie.github.io/images/201809/20180922_08.png)


## 开发细节总结

- 登录模块

看了一下bootdo的源码，发现在登录提交表单时，密码居然没有进行加密处理。本人通过Wireshark进行了抓包，轻松的获取了提交的用户名与密码，这是极其不安全的。

![https://AnonymousRookie.github.io/images/201809/20180922_09.png](https://AnonymousRookie.github.io/images/201809/20180922_09.png)

因此，在开发周报系统时，对登录提交的的密码通过MD5进行了简单的加密，当然数据库中的密码也是通过密文进行存储的。再次抓包发现密码是经过加密后的一长串字符，安全性至少稍微提高了那么一点。

![https://AnonymousRookie.github.io/images/201809/20180922_10.png](https://AnonymousRookie.github.io/images/201809/20180922_10.png)


- 周报填写模块

可以添加多个任务。


- 周报查询模块

在某一周填写的周报中一般包括多个任务，但是周数、工号以及姓名都是一样的，因此，像Excel一样对这些相同单元格进行了合并。


- 周报统计模块

除了要统计未提交周报的人员信息之外，还需要对那些已经提交但未按时提交周报的人员信息进行统计。


- 周报导出模块

直接采用了bootstrap table的导出功能。


## 服务器部署

目前，由于周报系统更新频率较低，因此采用的是手动部署的方式。当然，后期可以利用Jenkins来实现自动化部署。