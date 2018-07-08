---
layout: post
title:  "C++中字符串的常见操作"
categories: C/C++
tags: C++ 算法
author: ZhangJie
---

* content
{:toc}
在平时的开发过程中，经常会需要对一些字符串的进行各种处理，因此本文对一些常见的字符串操作进行了总结。主要包括：去除字符串两端的空格、判断字符串是否为数值、字符串转换大小写、字符串转换为数字、字符串是否包含某个前缀/后缀。




## 去除字符串两端的空格
- 方法1：

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
- 方法2：

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




## 判断字符串是否为数值

```cpp
bool isStringNumeric(const char* str)
{
    if (nullptr == str) {
        std::cout << "nullptr == str" << std::endl;
        return false;
    }
    // The first character may be '-'
    if ('-' == *str) {
        ++str;
    }
    // The first character after the sign must be a digit
    if (!isdigit(*str)) {
        return false;
    }
    ++str;
    // All remaining characters must be digits, with a single decimal (.) permitted
    unsigned int decimalCount = 0;
    while (*str) {
        if (!isdigit(*str)) {
            if ('.' == *str && 0 == decimalCount) {
                // Max of 1 decimal allowed
                ++decimalCount;
            } else {
                return false;
            }
        }
        ++str;
    }
    return true;
}
```

完整代码及测试代码详见：[004-is_string_numeric](https://github.com/AnonymousRookie/useful-codes/blob/master/c_and_cplusplus/004-is_string_numeric.cpp)


## 字符串转换大小写

```cpp
// 在原字符串基础上转换, 会修改原字符串
std::string& InPlaceLowercase(std::string& s)
{
    std::transform(s.begin(), s.end(), s.begin(), ::tolower);
    return s;
}

std::string& InPlaceUppercase(std::string& s)
{
    std::transform(s.begin(), s.end(), s.begin(), ::toupper);
    return s;
}

// 在新字符串基础上转换, 不会修改原字符串
std::string StrToLower(const std::string& s)
{
    std::string res(s);
    std::transform(res.begin(), res.end(), res.begin(), ::tolower);
    return res;
}

std::string StrToUpper(const std::string& s)
{
    std::string res(s);
    std::transform(res.begin(), res.end(), res.begin(), ::toupper);
    return res;
}
```

完整代码及测试代码详见：[008-string_tolower_toupper](https://github.com/AnonymousRookie/useful-codes/blob/master/c_and_cplusplus/008-string_tolower_toupper.cpp)




## 字符串转换为数字

- 方法1：

```c

const char digits[] = "9876543210123456789";
const char* zero = digits + 9;

template<typename T>
size_t convert(char buf[], T value)
{
    T i = value;
    char* p = buf;

    do {
        int lsd = static_cast<int>(i % 10);
        i /= 10;
        *p++ = zero[lsd];
    } while (i != 0);

    if (value < 0) {
        *p++ = '-';
    }
    *p = '\0';
    std::reverse(buf, p);

    return p - buf;
}

```

- 方法2：

```cpp
// Converts string to integral type T. If string is not convertible, T() is returned.
template<typename T, typename String>
T to_integral(String const& s, T const errorval = T())
{
    T ret{};

    auto it = s.cbegin();
    if (it != s.cend() && (*it == '-' || *it == '+')) {
        ++it;
    }

    if (it == s.cend()) {
        return errorval;
    }

    for (; it != s.cend(); ++it) {
        auto const& c = *it;
        if (c < '0' || c > '9') {
            return errorval;
        }
        ret *= 10;
        ret += c - '0';
    }

    if (!s.empty() && s.front() == '-') {
        return ret *= static_cast<T>(-1);
    }
    else {
        return ret;
    }
}

```

- 方法3：

```cpp
// number -> string
template <typename T>
std::string NumberToString(T value)
{
    std::ostringstream os;
    os << value;
    return os.str();
}
// string -> number
template <typename T>
inline T StringToNumber(const std::string& str)
{
    std::stringstream ss;
    T n = 0;
    ss << str;
    ss >> n;
    return n;
}
```

完整代码及测试代码详见：[010-string_number_convert](https://github.com/AnonymousRookie/useful-codes/blob/master/c_and_cplusplus/010-string_number_convert.cpp)



## 字符串是否包含某个前缀/后缀

- 方法1：

```cpp
// ----------------------------------------------------------------------
// HasPrefixString()
//    Check if a string begins with a given prefix.
// StripPrefixString()
//    Given a string and a putative prefix, returns the string minus the
//    prefix string if the prefix matches, otherwise the original
//    string.
// ----------------------------------------------------------------------
inline bool HasPrefixString(const string& str, const string& prefix)
{
    return str.size() >= prefix.size() &&
        str.compare(0, prefix.size(), prefix) == 0;
}

inline string StripPrefixString(const string& str, const string& prefix)
{
    if (HasPrefixString(str, prefix)) {
        return str.substr(prefix.size());
    } else {
        return str;
    }
}

// ----------------------------------------------------------------------
// HasSuffixString()
//    Return true if str ends in suffix.
// StripSuffixString()
//    Given a string and a putative suffix, returns the string minus the
//    suffix string if the suffix matches, otherwise the original
//    string.
// ----------------------------------------------------------------------
inline bool HasSuffixString(const string& str, const string& suffix)
{
    return str.size() >= suffix.size() &&
        str.compare(str.size() - suffix.size(), suffix.size(), suffix) == 0;
}

inline string StripSuffixString(const string& str, const string& suffix)
{
    if (HasSuffixString(str, suffix)) {
        return str.substr(0, str.size() - suffix.size());
    } else {
        return str;
    }
}
```

- 方法2：

```c
bool starts_with(const char* str, const char* pattern)
{
    if (!str || !pattern) {
        return false;
    }
    while (*pattern && *str == *pattern) {
        ++str;
        ++pattern;
    }
    return (*pattern == 0);
    }
```


完整代码及测试代码详见：[016-string_prefix_suffix](https://github.com/AnonymousRookie/useful-codes/blob/master/c_and_cplusplus/016-string_prefix_suffix.cpp)
