---
layout: post
title: "cmake学习笔记"
categories: cmake
tags: cmake
author: ZhangJie
---

* content
{:toc}
本文记录一些cmake学习笔记。




## cmake

- Centos7 安装 cmake

```sh
wget https://cmake.org/files/v3.10/cmake-3.10.2.tar.gz

tar -zxvf cmake-3.10.2.tar.gz  

./bootstrap --prefix=安装目录（默认为/usr/local）

gmake

make install
```

常见问题：

安装 CMake 错误: c++: internal compiler error: Killed (program cc1plus)


这个错误通常是因为 memory 不够, 解决方法增加 swap space。


- cmake使用

```
cmake -> make
CMakeLists.txt -> makefile
```

cmake指向CMakeLists.txt所在的目录，cmake后会生成很多编译的中间文件以及makefile文件，一般建议新建一个目录，专门用来编译：
```sh
mkdir build
cd build
cmake ..
make
```

```
# UNIX Makefile
cmake ..

# Mac OSX
cmake -G "Xcode" ..

# Microsoft Windows
cmake -G "Visual Studio 14" ..
cmake -G "Visual Studio 14 Win64" ..
```

- cmake中的一些预定义变量

```
PROJECT_SOURCE_DIR: 工程的根目录
```


## CMakeLists.txt

示例：

```
cmake_minimum_required(VERSION 3.10.1)

project(z_stl)

include_directories(src)

set(HEADERS
    src/defines.h
    src/z_string.h
    tests/z_string_test.h
)

set(SOURCES
    src/z_string.cpp
    tests/test.cpp
    tests/z_string_test.cpp
)

add_executable(z_stl ${HEADERS} ${SOURCES})
```


```
# 最低版本要求
cmake_minimum_required(VERSION 2.6)

# 查找指定目录下所有源文件, 将结果存入指定变量中
aux_source_directory(./ SRC_LIST)

# 根据SRC_LIST中的源文件编译生成可执行文件app
add_executable(app ${SRC_LIST})

# 设置生成路径
set(LIBRARY_OUTPUT_PATH ./)
set(EXECUTABLE_OUTPUT_PATH ./bin)


# 根据SRC_LIST1、SRC_LIST2中的源文件编译生成静态链接库base
add_library(base STATIC ${SRC_LIST1} ${SRC_LIST2})
```


