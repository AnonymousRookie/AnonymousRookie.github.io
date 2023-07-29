---
layout: post
title:  "python学习笔记"
categories: python
tags: python
author: ZhangJie
---

* content
{:toc}
python学习笔记。




## 基础知识

### 单例模式的4种实现

- 使用__new__方法
```py
class Singleton(object):
    def __new__(cls, *args, **kw):
        if not hasattr(cls, '_instance'):
            orig = super(Singleton, cls)
            cls._instance = orig.__new__(cls, *args, **kw)
        return cls._instance

class MyClass(Singleton):
    a = 1
```

- 共享属性

创建实例时把所有实例的__dict__指向同一个字典,这样它们具有相同的属性和方法.
```py
class Borg(object):
    _state = {}
    def __new__(cls, *args, **kw):
        ob = super(Borg, cls).__new__(cls, *args, **kw)
        ob.__dict__ = cls._state
        return ob

class MyClass2(Borg):
    a = 1
```

- 装饰器版本
```py
def singleton(cls):
    instances = {}
    def getinstance(*args, **kw):
        if cls not in instances:
            instances[cls] = cls(*args, **kw)
        return instances[cls]
    return getinstance

@singleton
class MyClass:
  ...
```

- import方法 

作为python的模块是天然的单例模式

```py
# mysingleton.py
class My_Singleton(object):
    def foo(self):
        pass

my_singleton = My_Singleton()

# to use
from mysingleton import my_singleton

my_singleton.foo()
```


### Python里的拷贝

引用和copy(),deepcopy()的区别

```py
import copy
a = [1, 2, 3, 4, ['a', 'b']]  #原始对象

b = a  #赋值，传对象的引用
c = copy.copy(a)  #对象拷贝，浅拷贝
d = copy.deepcopy(a)  #对象拷贝，深拷贝

a.append(5)  #修改对象a
a[4].append('c')  #修改对象a中的['a', 'b']数组对象

print 'a = ', a
print 'b = ', b
print 'c = ', c
print 'd = ', d

输出结果：
a =  [1, 2, 3, 4, ['a', 'b', 'c'], 5]
b =  [1, 2, 3, 4, ['a', 'b', 'c'], 5]
c =  [1, 2, 3, 4, ['a', 'b', 'c']]
d =  [1, 2, 3, 4, ['a', 'b']]
```


### Python的is

is是对比地址，==是对比值。



### python有序字典

```py
import collections
# 通过OrderedDict类创建的字典是有序的
dic = collections.OrderedDict()
```


### 遍历list同时输出index

```py
for index, val in enumerate(list):
    pass
```


### 数字转换成字符串，前面补0

```py
for conIndex in range(conNum):
    strIndex = str(conIndex).zfill(3)  # 1 -> 001
```


### list class 多个关键字 排序

```py
from operator import itemgetter, attrgetter
# 对netHost.ethernetList进行排序
class IpAddr:
    def __init__(self):
        self.Ip = ''
        self.LinkType = ''
        self.Port = ''

class Ethernet:
    def __init__(self):
        self.linkType = ''
        self.netType = ''
        self.AorB = ''
        self.ipAddrList = []  # the list of IpAddr

class NetHost:
    def __init__(self):
        self.name = ''
        self.hostType = ''
        self.ethernetList = []  # the list of Ethernet
        
print('\n---before sort---\n')
for tmpEthernet in netHost.ethernetList:
    print (tmpEthernet.linkType, tmpEthernet.AorB)

netHost.ethernetList.sort(key=attrgetter('linkType', 'AorB'))

print('\n---after sort---\n')
for tmpEthernet in netHost.ethernetList:
    print (tmpEthernet.linkType, tmpEthernet.AorB)
    
```

## 有趣的代码实现

- 打印 I LOVE U

```py
y = 2.5

while y >= -1.6:
    x = -3
    while x <= 4.8:
        b1 = (x*x + y*y - 1)*(x*x + y*y - 1)*(x*x + y*y - 1) <= 3.6*x*x*y*y*y
        b2 = x>-2.4 and x <-2.1 and y<1.5 and y>-1
        b3 = ((x<2.5 and x>2.2) or (x>3.4 and x<3.7)) and y>-1 and y<1.5
        b4 = y>-1 and y<-0.6 and x<3.7 and x>2.2

        if b1 or b2 or b3 or b4:
            print('*',end='')
        else:
            print(' ',end='')

        x = x+0.1

    y = y-0.2
    print('');
```


```
C:\>python test.py


      ***           *****           *****           ***         ***
      ***         **********     **********         ***         ***
      ***        ************* *************        ***         ***
      ***        ***************************        ***         ***
      ***        ***************************        ***         ***
      ***         *************************         ***         ***
      ***          ***********************          ***         ***
      ***           *********************           ***         ***
      ***            *******************            ***         ***
      ***              ***************              ***         ***
      ***                ***********                ***         ***
      ***                   *****                   ***************
      ***                     *                     ***************
```








