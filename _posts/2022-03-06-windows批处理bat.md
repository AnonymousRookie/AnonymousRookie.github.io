---
layout: post
title: "Windows批处理"
categories: Windows 批处理 bat
tags: Windows 批处理 bat
author: ZhangJie
---

* content
{:toc}
本文记录一些常用的Windows批处理（bat）命令。




## 常见命令

### 基本命令

```
::注释

::关闭回显，在批处理运行命令的时候不会一条一条的显示执行的命令
@echo off

::设置cmd窗口的标题
title "I'm a title"

echo "Hello bat!"

::输出空行，即相当于输入一个回车，注意echo和.之间没有空格
echo.


::指定控制台输出内容的颜色属性, 2 = 绿色
color 2 

::切换到当前bat文件所在目录
cd /d %~dp0 

pause
```

- 跳转

```
goto SUCCESS

:FAILED

color 4
echo "Build failed!"
title "Build failed!"
pause
exit

:SUCCESS
color 2
echo "Build success."
title "Build success."
```


### 文件操作

```
set BinFolder=dist
set PyqtPlatformsFolder="D:/ProgramFiles/python3.4.3/Lib/site-packages/PyQt5/plugins"
set PycacheFolder=__pycache__

if exist %BinFolder% (
    rd /s /Q %BinFolder%
)

::删除当前目录及子目录中所有包含%PycacheFolder%的文件夹
for /f "tokens=*" %%i in ('dir/b/ad/s^|findstr "%PycacheFolder%"') do (
    rd /s /Q "%%i"
)


set PyFileGeneratedByUI="ui_designer.py"
if exist %PyFileGeneratedByUI% (
    del /s /Q %PyFileGeneratedByUI%
)

xcopy %PyqtPlatformsFolder%\platforms %BinFolder%\platforms\ /c/q/e


::创建文件夹
set folder1=LuaScript
md %folder1%
```


### 延时启动进程

```
@echo off
if "%1" == "h" goto begin
mshta vbscript:createobject("wscript.shell").run("%~nx0 h",0)(window.close)&&exit
:begin

::延时30秒
timeout /nobreak /t 30

start E:\xxx.exe
```
 



