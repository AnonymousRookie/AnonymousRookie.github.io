---
layout: post
title:  "计算机网络学习笔记"
categories: 计算机网络
tags: 计算机网络
author: ZhangJie
---

* content
{:toc}
计算机网络学习笔记。




## 计算机网络基础知识

#### OSI七层模型

- 物理层：机械、电子、定时接口通信信道上的原始比特流传输。
- 数据链路层：物理寻址，同时将原始比特流转变为逻辑传输线路。
- 网络层：控制子网的运行，如逻辑编址、分组传输、路由选择。
- 传输层：接受上一层的数据，在必要的时候把数据进行分割，并将这些数据交给网络层，且保证这些数据段有效到达对端。
- 会话层：不同机器上的用户之间建立及管理会话。（安全套接字层协议SSL、传输层安全协议TLS、远程过程调用协议RPC）
- 表示层：信息的语法语义以及它们的关联，如加密解密、转换翻译、压缩解压缩。
- 应用层：各种应用程序协议，如HTTP、FTP、SMTP、SNMP。



#### ARP

- ARP协议的作用及其工作原理，什么是ARP欺骗攻击？

ARP是地址解析协议，根据IP地址获得MAC地址。
原理：当两台主机A和B通信的时候，如果A要向B发送信息，会先查询本地主机ARP缓存表，将B的ip解析为B的MAC地址，然后进行数据传输。如果没有找到，则A主机广播一个ARP请求，请求IP地址为B主机IP的物理地址。主机B识别自己的IP地址，向A主机发回一个ARP响应报文。其中就包含有B的MAC地址，A接收到B的应答后，就会更新本地的ARP缓存，接着使用这个MAC地址发送数据。

ARP欺骗：局域网中的机器B向A发送一个自己伪造的ARP应答，如果这个应答是B冒充C伪造来的，即IP地址为C的IP，而MAC地址是伪造的，则当A接收到B伪造的ARP应答后，就会更新本地的ARP缓存，这样在A看来C的IP地址没有变，而它的MAC地址已经不是原来那个了。当A再要向C发送信息的时候，就会发送到B，而不是再发向C。


#### VLAN

VLAN（Virtual Local Area Network）虚拟局域网，把大的局域网划分为几个单独的互不相通的虚拟局域网，隔离广播风暴。

虚拟局域网（VLAN）是一组逻辑上的设备和用户，这些设备和用户并不受物理位置的限制，可以根据功能、部门及应用等因素将它们组织起来，相互之间的通信就好像它们在同一个网段中一样，由此得名虚拟局域网。VLAN是一种比较新的技术，工作在OSI参考模型的第2层和第3层，一个VLAN就是一个广播域，VLAN之间的通信是通过第3层的路由器来完成的。与传统的局域网技术相比较，VLAN技术更加灵活，它具有以下优点： 网络设备的移动、添加和修改的管理开销减少；可以控制广播活动；可提高网络的安全性。
在计算机网络中，一个二层网络可以被划分为多个不同的广播域，一个广播域对应了一个特定的用户组，默认情况下这些不同的广播域是相互隔离的。不同的广播域之间想要通信，需要通过一个或多个路由器。这样的一个广播域就称为VLAN。

## TCP

- TCP的包是没有IP地址的，那是IP层上的事，但是有源端口和目的端口。
- Sequence Number是包的序号，用来解决网络包乱序（reordering）问题。
- Acknowledgement Number就是ACK，用于确认收到，用来解决不丢包的问题。


#### 什么是socket?

socket 可以看成是用户进程与内核网络协议栈的编程接口。

#### 大端/小端


不同主机有不同的字节序。
网络字节序规定为大端字节序。

```
---> 低字节方向 
int  0x 12 34 56 78

---> 内存增长方向
大端字节序(Big Endian)
[12][34][56][78]

小端字节序(Little Endian)
[78][56][34][12]
```

```cpp
// 判断当前系统是大端模式还是小端模式
void main()
{
    unsigned int data = 0x12345678;
	char* p = &data;
	
	printf("%x, %x, %x, %x\n", p[0], p[1], p[2], p[3]);
	if (p[0] == &78) {
	    printf("当前系统是小端模式\n");
	} else {
	    printf("当前系统是大端模式\n");
	}
}
```



#### 三次握手 与 四次挥手

- 三次握手：发送方发送一个SYN到接收方请求建立连接，接收方返回一个ACK确认收到请求，并携带一个SYN给发送方请求建立双向连接，发送方再返回一个ACK给接收方确认，这个时候连接就建立了。

- 四次挥手：发送方发送一个FIN给接收方主动请求断开连接，接收方返回一个ACK确认，接着接收方再发送一个FIN请求断开另一方向的连接，发送方收到之后返回一个ACK确认。这个时候，连接就中断了。


为什么建链接要3次握手，断链接需要4次挥手？

- 对于建链接的3次握手，主要是要初始化Sequence Number 的初始值。通信的双方要互相通知对方自己的初始化的Sequence Number，所以叫SYN，全称Synchronize Sequence Numbers。这个号要作为以后的数据通信的序号，以保证应用层接收到的数据不会因为网络上的传输的问题而乱序（TCP会用这个序号来拼接数据）。

- 对于4次挥手，其实你仔细看是2次，因为TCP是全双工的，所以，发送方和接收方都需要Fin和Ack。只不过，有一方是被动的，所以看上去就成了所谓的4次挥手。如果两边同时断连接，那就会就进入到CLOSING状态，然后到达TIME_WAIT状态。


#### 常见API

##### socket函数

```cpp
#include <sys/socket.h>

int socket(int domain, int type, int protocol);
```

socket函数在成功返回时，返回一个小的非负整数值，它与文件描述字类似，我们称其为套接口描述字（socket descriptor），简称套接字（sockfd）。

The domain argument specifies a communication domain; this selects the protocol family which will be used for communication. 

- AF_INET: IPv4 Internet protocols

The socket has the indicated type, which specifies the communication semantics.

- SOCK_STREAM: Provides sequenced, reliable, two-way, connection-based byte streams.  An out-of-band data transmission mechanism may be supported.

- SOCK_DGRAM: Supports datagrams (connectionless, unreliable messages of a fixed maximum length).

```
AF_XXX：AF_前缀代表地址族；
PF_XXX：PF_前缀代表协议族。
```

##### connect函数

```cpp
#include <sys/socket.h>
int connect(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
```
sockfd是由socket函数返回的套接字。

如果是TCP套接口的话，connect激发TCP的三次握手过程，仅在连接建立成功或出错时才返回，返回的错误可能有以下几种情况：

1. 如果client没有收到SYN分节的响应，则返回ETIMEDOUT。

当调用connect时，发出一个SYN，若无响应，等待6秒后再发一个，若仍无响应，24秒后再发一个，若总共等了75秒之后仍未收到响应，则返回错误。

2. 如果server对client的SYN响应是RST，则表明该server主机在我们指定的端口上没有进程在等待与之连接（例如服务器进程也许没有启动），这称为硬错（hard error），客户端一收到RST，马上就返回错误ECONNREFUSED。

RST的含义为“复位”，有三个条件可以产生RST：SYN到达某端口但此端口上没有正在监听的服务器、TCP想取消一个已有连接、TCP接收到了一个根本不存在的连接上的分节。

3. 如果某客户端发出的SYN在中间的路由器上引发了一个目的地不可达ICMP错误，这称为软错（soft error）。客户端上的内核保存此消息，并按第一种情况中的时间间隔连续发出SYN，若规定时间仍未收到响应，则把保存的消息（即ICMP错误）作为EHOSTUNREACH或ENETUNREACH错误返回进程。

根据TCP状态转换图，函数connect导致从CLOSED状态（调用函数socket创建套接口后就一直处于此状态）转到SYN_SENT状态，若成功再转到ESTABLISHED状态。如果connect失败，则套接口不可再用，必须关闭，不能再对此套接口调用coonnect。


##### bind函数

```cpp
#include <sys/socket.h>
int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
```

若指定端口号为0，调用函数bind时，内核选择一个临时端口；但若指定一个通配IP地址（IPADDR_ANY，其值一般为0，它通知内核选择IP地址），则直到套接口已连接（TCP）或数据报已在套接口上发出（UDP），内核才选择一个本地的IP地址。


注意：如果让内核来为套接口选择一个临时端口号，那么必须注意，函数bind并不返回所选择的值。函数参数const struct sockaddr *addr为const，无法返回所选之值，为了得到内核所选择的这个临时端口值，必须调用函数getsockname来返回协议地址。


函数bind返回的一个常见错误是EADDRINUSE（地址已使用）。
可以使用套接口选项SO_REUSEADDR和SO_REUSEPORT进行相关设置。


##### listen函数

```cpp
#include <sys/socket.h>
int listen(int sockfd, int backlog);
```

函数listen仅被TCP服务器调用，它做两件事情：

1. 当函数socket创建一个套接口时，它被假设为一个主动套接口，也就是说，它是一个将调用connect发起连接的客户端套接口，函数listen将未连接的套接口转换成被动套接口，指示内核应接受向此套接口的连接请求。根据TCP状态转换图，调用listen导致套接口从CLOSED状态转换到LISTEN状态。
2. 函数的第二个参数规定了内核为此套接口排队的最大连接个数。

为了理解参数bcaklog，我们必须明白，对于给定的监听套接口，内核要维护两个队列：

- 未完成连接队列，为每个这样的SYN分节开设一个条目：已经由客户端发出并到达服务器，服务器正在等待完成三次握手，这些套接口都处于SYN_RCVD状态。
- 已完成连接队列，为每个已经完成三次握手的客户端开设一个条目，这些套接口都处于ESTABLISHED状态。


SYN flooding攻击：写一个程序，以很高的速率发送SYN，装填一个甚至多个TCP端口的未完成连接队列，这样，通过非法的SYN装填未完成连接队列，使合法的SYN排不上队，导致合法客户端的服务被拒绝（denial of service）。

##### accept函数

一般情况下，服务器进程在调用accept函数后处于睡眠状态，等待客户端的连接和内核对它的接受。

当三次握手建立连接后，accept函数返回一个称为“已连接描述符(connected descriptor)”的新描述字(connfd)，connfd用于与新客户端进行通信。

accept为每个连接到服务器的客户端返回一个新的已连接描述字。

```cpp
#include <sys/socket.h>
int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen);
```

如果函数accept执行成功，则返回值是由内核自动生成的一个全新描述字，代表与客户端的TCP连接。

accept函数的第一个参数称为监听套接口（listening socket）描述字，把它的返回值称为已连接套接口（connected socket）描述字。

一个给定的服务器通常只生成一个监听套接口并且一直存在，直到该服务器关闭。内核为每个被接受的客户端连接创建一个已连接套接口，当服务器完成某客户的服务时，关闭已连接套接口。



##### close函数

```cpp
#include <unistd.h>
int close(int fd);
```
当并发服务器中父进程关闭已连接套接口时，它只是将描述字的访问计数值减1，由于访问计数值仍大于0，这次close调用并不引发TCP的四分组连接终止序列。

如果确实想对TCP连接发一个FIN，可以改用函数shutdown。


##### shutdown函数

终止网络连接的正常方法是调用close，但是close有2个限制可由shutdown来避免。
1. close将描述字的访问计数减1，仅在此计数为0时才关闭套接口。用shutdown可以激发TCP的正常连接终止序列，而不管访问计数。

2. close终止了数据传送的两个方向：读和写。可以调用shutdown关闭一半的TCP连接：

```cpp
#include <sys/socket.h>
int shutdown(int sockfd, int howto);
```

- SHUT_RD: 关闭连接的读这一半，不再接收套接口中的数据且现留在套接口接收缓冲区中的数据都作废，进程不能再对套接口执行任何的读函数。
- SHUT_WR：关闭连接的写这一半，在TCP场合下，这称为半关闭（half-close），当前留在套接口发送缓冲区中的数据都被发送，后跟正常的TCP连接终止序列。进程不能再对套接口执行任何的写函数。
- SHUT_RDWR：连接的读和写都关闭。
 


#### 存在TIME_WAIT状态的2个理由

1. 实现终止TCP全双工连接的可靠性

假设最后一个ACK丢失，对方将重发最终的FIN，因此客户端必须维护状态信息以允许它重发最终的ACK。如果不维护状态信息，它将响应以RST，而对方则把它解释成一个错误。如果TCP打算执行所有必要的工作以彻底终止某个连接上两个方向的数据流（即全双工关闭），那么它必须正确处理连接终止序列四个分节中任何一个分节的丢失情况。这也说明了执行主动关闭的一端为何进入TIME_WAIT状态，因为它可能不得不重发最终的ACK。

2. 允许老的重复分节在网络中消逝

假设存在一个TCP连接，我们关闭这个连接后，在以后某个时候又重新建立起相同的IP和Port之间的TCP连接，后一个连接称为前一个连接的化身，因为他们的IP和Port都相同。TCP必须防止来自某个连接的老重复分组在连接终止后再现，从而被误解成属于同一连接的化身。要实现这种功能，TCP不能给处于TIME_WAIT状态的连接启动新的化身。既然TIME_WAIT状态的持续时间是2MLS，这就足够让某个方向上的分组最多存活MSL秒即被丢弃，另一个方向上的应答最多存活MSL秒也被丢弃。通过实施这个规则，我们能够保证当成功建立一个TCP连接时，来自该连接先前化身的老重复分组都已经在网络中消逝。


#### 缓冲区大小及限制

IPv4数据报的最大大小是65535字节，包括IPv4头部。

最大传输单元MTU(maximum transmission unit)，由硬件规定。
以太网的MTU为1500字节。IPv4要求的最小MTU是68字节。

两台主机间的路径上的最小MTU称为路径MTU（path MTU）。1500字节的以太网MTU是当今常见的路径MTU。

当一个IP数据报从某个接口发出时，如果它的大小超过相应链路的MTU，IPv4将执行分片（fragmentation），各片段到达目的地前不会被重组。


IPv4定义了最小重组缓冲区大小（minimum reassembly buffer size）: 任何IPv4的实现都必须支持的最小数据报大小，对于IPv4其值为576字节。


TCP有一个MSS，用于向对方TCP通告对方在每个分节中能发送的最大TCP数据量。SYN分节带有MSS选项，MSS的目的是告诉对方其重组缓冲区大小的实际值，从而避免分片。MSS经常设置成MTU减去IP和TCP头部的固定长度，在以太网中使用IPv4的MSS为1460。


#### 常见socket选项

- TCP_NODELAY

Nagle算法主要是用来避免大量的小数据包在网络中传输，从而降低网络容量利用率。比如一个20字节的TCP首部+20字节的IP首部+1个字节的数据组成的TCP数据报，有效传输通道利用率只有将近1/40。如果网络充斥着这样的小分组数据，则网络资源的利用率是相当低下的。但是对于一些需要小包场景的程序，比如像telnet或ssh这样的交互性比较强的程序，你需要关闭这个算法。可以在Socket设置TCP_NODELAY选项来关闭这个算法。





## I/O模型

Unix中五个I/O模型的基本区别：

- 阻塞I/O模型
假设函数recvfrom为系统调用，进程调用recvfrom，此系统调用直到数据报到达且拷贝到应用缓冲区或者出错才返回。

- 非阻塞I/O模型
轮询（polling），应用进程连续不断地查询内核，看看某操作是否准备好，这对CPU时间是极大地浪费。

- I/O复用(select和poll)模型
调用select或poll，在这两个系统调用的某一个上阻塞，而不是阻塞在真正的I/O系统调用。当select返回套接口可读条件时，我们调用recvfrom将数据拷贝到应用缓冲区中。
使用select的好处在于我们可以等待多个描述字准备好。

- 信号驱动I/O(SIGIO)模型
通过系统调用sigaction安装一个信号处理程序，此系统调用立即返回，进程继续工作，它是非阻塞的，当数据报准备好被读时，就为该进程生成一个SIGIO信号，我们随即可以在信号处理程序中调用recvfrom来读数据报。

- 异步I/O模型
让内核启动操作，并在整个操作完成后通知我们。与信号驱动I/O的区别：信号驱动I/O是内核通知我们何时可以启动一个I/O操作；而异步I/O是由内核通知我们I/O操作合适完成。


## select与epoll


#### select与epoll的区别

- epoll和select都是I/O多路复用的技术，都可以实现同时监听多个I/O事件的状态。
- epoll相比select效率更高，主要是基于其操作系统支持的I/O事件通知机制，而select是基于轮询机制。



