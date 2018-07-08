---
layout: post
title:  "C++中字符串的常见操作"
categories: C/C++
tags: C++ 算法
author: ZhangJie
---

* content
{:toc}
在平时的开发过程中，经常会需要对一些字符串的进行各种处理，因此本文对一些常见的字符串操作进行了总结。主要包括：去除字符串两端的空格、判断字符串是否为数值、字符串转换大小写、字符串转换为数字、字符串是否以某个前缀/后缀结尾。




## 去除字符串两端的空格
- 方法一：

```cpp
// trim from start(去除字符串左端空格)
std::string& ltrim(std::string& s)
{
    s.erase(s.begin(), std::find_if(s.begin(), s.end(), std::not1(std::ptr_fun<int, int>(std::isspace))));
    return s;
}

// trim from end(去除字符串右端空格)
std::string& rtrim(std::string& s)
{
    s.erase(std::find_if(s.rbegin(), s.rend(), std::not1(std::ptr_fun<int, int>(std::isspace))).base(), s.end());
    return s;
}

// trim from both ends(去除字符串两端空格)
std::string& trim(std::string& s)
{
    return ltrim(rtrim(s));
}
```
- 方法二：

```c
// 去除char* 字符串两端空格
char* trimWhiteSpace(char* str)
{
    if (str == NULL) {
        return str;
    }
    char* end;
    // Trim leading space.
    while (isspace(*str))
        str++;
    // All spaces
    if (*str == 0) {
        return str;
    }
    // Trim trailing space.
    end = str + strlen(str) - 1;
    while (end > str && isspace(*end))
        end--;
    // Write new null terminator.
    *(end+1) = 0;
    return str;
}
```

完整代码及测试代码详见：[002-trimming_functions](https://github.com/AnonymousRookie/useful-codes/blob/master/c_and_cplusplus/002-trimming_functions.cpp)

