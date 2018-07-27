---
layout: post
title:  "快速开发PyQt程序的脚手架"
categories: PyQt 脚手架
tags: PyQt 脚手架
author: ZhangJie
---

* content
{:toc}
平时经常需要写一些小工具，例如：配置自动生成工具、日志解析工具等。大部分工具对于性能并没有特殊要求，为了提高开发效率，个人比较偏爱通过PyQt来进行开发。本文主要介绍个人编写的一个能够快速开发PyQt程序的脚手架。




编写python等脚本程序，个人比较喜欢Sublime Text 和 Visual Studio Code，
利用这些文本编辑器开发PyQt程序通常需要一步步手动进行编写：

- 编写.ui文件，可以通过Qt Creator设计并生成；
- 在cmd中输入指定命令根据.ui文件生成.py文件；
- 编写.qrc文件，编写.qss文件；
- 在cmd中输入指定命令根据.qrc文件生成.py文件；
- 编写.py文件，在main函数中创建并显示窗体；
- 通过命令打包生成.exe文件。

如果每次编写一个工具都按照上述步骤手动执行，严重浪费时间。

我们都知道当编写一个MFC程序/基于C++的QT窗体程序时，可以直接通过VS2015 New Project -> MFC Application / Qt GUI Application，编译即可生成一个最基本的MFC / Qt窗体程序，接下来只要在此基础上添加控件以及事件响应处理函数即可。

那么，为了提高开发效率，个人编写了一个能够快速开发PyQt程序的脚手架，主要是通过批处理对上述步骤进行封装，下次一键即可生成一个最基本的PyQt窗体程序，接下来只要根据需求添加控件，编写事件响应处理函数即可，修改完成后再一键即可重新生成可执行程序。



完整程序详见：[pyqt_gui_exe](https://github.com/AnonymousRookie/SmallFrameWorks/tree/master/pyqt_gui_exe)


程序目录结构如下：

```
pyqt_gui_exe/
├── autobuild.bat
├── autoclean.bat
├── FACE.ICO
├── __init__.py
├── main.py
├── py2exe.bat
├── py2exe.spec
├── qrc2py.bat
├── qss
│   └── mystyle.qss
├── README
├── setup.py
├── stylesheet.qrc
├── ui2py.bat
└── ui_designer.ui
```

运行autobuild.bat即可一键生成.exe可执行文件。
autobuild.bat的详细内容如下：

```c
@echo off

title "Build start..."
echo "Build start..."

color 2

cd /d %~dp0

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
call "ui2py.bat"

set errorInfo=Generate %outputFile% failed.
set successInfo=Generate %outputFile% successfully.

if %errorlevel%==1 (
    echo %errorInfo%
    goto FAILED
) else (
    echo %successInfo%
)
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
call "qrc2py.bat"

set errorInfo=Generate %outputFile% failed.
set successInfo=Generate %outputFile% successfully.

if %errorlevel%==1 (
    echo %errorInfo%
    goto FAILED
) else (
    echo %successInfo%
)
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
call "py2exe.bat"

set errorInfo=Generate executable file failed!
set successInfo=Generate executable file successfully.

if %errorlevel%==1 (
    echo %errorInfo%
    goto FAILED
) else (
    echo %successInfo%
)
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
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

pause

```

通过上述脚本可以发现执行autobuild.bat，会依次执行ui2py.bat、qrc2py.bat、py2exe.bat，通过文件名很容易猜到各个批处理的作用，其中ui2py.bat可以根据.ui生成.py，qrc2py.bat可以根据.qrc生成.py，py2exe.bat根据.py生成.exe可执行程序文件。


运行autoclean.bat可以对autobuild.bat执行产生的文件进行清理，autoclean.bat的详细内容如下：

```c
@echo off

title "auto clean"
echo "clean start..."

color 2
cd /d %~dp0

set BinFolder=dist
set BuildTempFolder=build
set PycacheFolder=__pycache__

if exist %BinFolder% (
    rd /s /Q %BinFolder%
)

if exist %BuildTempFolder% (
    rd /s /Q %BuildTempFolder%
)

if exist %PycacheFolder% (
    rd /s /Q %PycacheFolder%
)

set PyFileGeneratedByUI="ui_designer.py"
set PyFileGeneratedByQrc="stylesheet.py"

if exist %PyFileGeneratedByUI% (
    del /s /Q %PyFileGeneratedByUI%
)

if exist %PyFileGeneratedByQrc% (
    del /s /Q %PyFileGeneratedByQrc%
)

echo "clean end."

pause
```

