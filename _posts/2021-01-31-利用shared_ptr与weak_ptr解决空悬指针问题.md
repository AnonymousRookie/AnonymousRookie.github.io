---
layout: post
title:  "利用shared_ptr与weak_ptr解决空悬指针问题"
categories: C/C++
tags: C++ shared_ptr weak_ptr 空悬指针
author: ZhangJie
---

* content
{:toc}
在多线程开发中，有两个指针p1和p2，指向堆上的同一个对象obj，    当对象obj被线程A通过指针p1销毁后， p2就变成了空悬指针，当线程B通过指针p2访问对象obj时，如何判断对象obj是否存活呢？通过C++11中的std::shared_ptr与std::weak_ptr能够非常容易的解决该问题。



## 空悬指针问题示例

```cpp
std::vector<Observer*> obs;
Observer* o1 = new Observer("o1");
obs.push_back(o1);

delete o1;     // 销毁o1指向的对象
o1 = nullptr;  // 置nullptr没啥用

for (auto o : obs)
{
    if (o)   // 判断是否为nullptr没啥用
    {
        o->update();   // 0xC0000005: 读取位置 0xDDDDDDDD 时发生访问冲突。
    }
}
```

## 空悬指针问题解决示例

```cpp
std::vector<std::weak_ptr<Observer>> obs;

{
    std::shared_ptr<Observer> o1 = std::make_shared<Observer>("o1");
    obs.push_back(o1);  // 离开本作用域，o1指向的对象即将销毁
}
    
for (auto o : obs)
{
    std::shared_ptr<Observer> spo(o.lock());  // 尝试提升，线程安全
    if (spo) 
    {
        // 提升成功    
        spo->update();  // spo在栈上，对象不可能在本作用域内销毁
    }
}
```