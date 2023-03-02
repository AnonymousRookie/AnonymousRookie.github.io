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


#### TCP/IP五层模型

- 物理层
- 数据链路层
- 网络层
- 传输层
- 应用层

#### ARP

- ARP协议的作用及其工作原理，什么是ARP欺骗攻击？

ARP是地址解析协议，根据IP地址获得MAC地址。
原理：当两台主机A和B通信的时候，如果A要向B发送信息，会先查询本地主机ARP缓存表，将B的ip解析为B的MAC地址，然后进行数据传输。如果没有找到，则A主机广播一个ARP请求，请求IP地址为B主机IP的物理地址。主机B识别自己的IP地址，向A主机发回一个ARP响应报文。其中就包含有B的MAC地址，A接收到B的应答后，就会更新本地的ARP缓存，接着使用这个MAC地址发送数据。

ARP欺骗：局域网中的机器B向A发送一个自己伪造的ARP应答，如果这个应答是B冒充C伪造来的，即IP地址为C的IP，而MAC地址是伪造的，则当A接收到B伪造的ARP应答后，就会更新本地的ARP缓存，这样在A看来C的IP地址没有变，而它的MAC地址已经不是原来那个了。当A再要向C发送信息的时候，就会发送到B，而不是再发向C。


#### VLAN

VLAN（Virtual Local Area Network）虚拟局域网，把大的局域网划分为几个单独的互不相通的虚拟局域网，隔离广播风暴。

虚拟局域网（VLAN）是一组逻辑上的设备和用户，这些设备和用户并不受物理位置的限制，可以根据功能、部门及应用等因素将它们组织起来，相互之间的通信就好像它们在同一个网段中一样，由此得名虚拟局域网。VLAN是一种比较新的技术，工作在OSI参考模型的第2层和第3层，一个VLAN就是一个广播域，VLAN之间的通信是通过第3层的路由器来完成的。与传统的局域网技术相比较，VLAN技术更加灵活，它具有以下优点： 网络设备的移动、添加和修改的管理开销减少；可以控制广播活动；可提高网络的安全性。
在计算机网络中，一个二层网络可以被划分为多个不同的广播域，一个广播域对应了一个特定的用户组，默认情况下这些不同的广播域是相互隔离的。不同的广播域之间想要通信，需要通过一个或多个路由器。这样的一个广播域就称为VLAN。


#### 滑动窗口

滑动窗口的存在是为了控制网络上包的数量。如果没有滑动窗口，那么就是一个很理想很理想的情况，发送方一有数据，加上包头达到MTU大小，直接发送，就和冲锋枪一样，突突突突。但是呢？这样子，实际上，没有考虑到接收方是否能接收完。接收端就像一个一直在吃饭的胖子，他的吃饭速度是固定的，它一次性最多能吃10碗饭，某个时刻可能已经吃了两碗饭，但是还没消化。所以这个时候，它只能再吃8碗饭了，如果这个时候你一下子给它80碗，必然导致它堵死了，吃不下不吃下。这个滑动窗口就是接收端告诉发送端我还能吃几碗饭的通话器。
总结下，这里已经有两个条件限制发送方的效率了，一个是MTU，全链路MTU大小，限制每次最大发送的包的大小。另一个是滑动窗口，限制发送方一次发送的包数量。


主要作用:滑动窗口主要是进行流量控制的。
如果发送端发送的速度较快，接收端接收到数据后处理的速度较慢，而接收缓冲区的大小是固定的，就会导致接收缓冲区满而丢失数据。TCP协议通过“滑动窗口(Sliding Window)”机制解决这一问题。


#### SNMP所采用的传输层协议是什么？

SNMP：简单网络管理协议，传输层使用UDP。


#### 常用命令

```
ipconfig                 // 查看Windows IP信息
ipconfig /all            // 查看Windows IP详细信息
ping -t 目标IP           // 一直ping
ping -n 数字 目标IP      // 指定ping包数量
nslookup www.baidu.com  // 手工解析域名的IP地址
```

#### 其他

- IP地址的表示方法 

IP地址的表示方法有两种：二进制和点分十进制。
IP地址是32位二进制数字，为方便阅读和从键盘上输入，可把每8位二进制数字转换成一个十进制数字，并用小数点隔开，这就是点分十进制。




## TCP和UDP

- TCP的包是没有IP地址的，那是IP层上的事，但是有源端口和目的端口。
- Sequence Number是包的序号，用来解决网络包乱序（reordering）问题。
- Acknowledgement Number就是ACK，用于确认收到，用来解决不丢包的问题。


### UDP

用户数据报协议。UDP是一种无连接协议。不可靠，不能保证每一UDP数据报可以到达目的地。

UDP注意点：
- UDP报文可能会丢失、重复；
- UDP报文可能会乱序；
- UDP缺乏流量控制：UDP缓冲区写满之后，没有流量控制机制，会覆盖缓冲区；
- UDP协议数据报文截断：如果接收到的数据报，大于缓冲区，报文可以被截断，后面的部分会丢失。
- recvfrom返回0，不表示连接关闭，因为UDP是无连接的。sendto可以发送数据0包，只含UDP头部。


UDP 的使用范围很窄，而且编程比 TCP 难多了。总之：使用 UDP 需要有强大到不容置疑的理由，when in doubt, use TCP.

可以用 UDP 的情况：实时音视频是可以而且应该用 UDP 的，一方面因为它常常涉及到网络穿透，另外一方面它不需要重传。我需要实时的看到你的图像跟声音，至于中间丢一帧什么的完全不重要。而为了重传往往会造成延迟与不同步，考虑一下，某一帧因为重传，导致0.5秒以后才到，那么整个音视频就延迟了0.5秒。考虑一下接收方看视频，如果使用 TCP 导致视频的中间延迟了0.5秒，只要我不按「快进」键，那么后续的视频全都会比发送方延迟0.5秒。这种延迟是累加的，随着持续丢帧，延迟会越来越大，达到数秒，甚至分钟级，这会严重影响实时音视频的用户体验。因此「实时音视频聊天」功能通常都会使用 UDP 实现。


#### recvfrom和sendto函数
```c
#include <sys/socket.h>
ssize_t recvfrom(int sockfd, void *buff, size_t nbytes, int flags, struct sockaddr *from, socklen_t *addrlen);
ssize_t sendto(int sockfd, const void *buff, size_t nbytes, int flags, const struct sockaddr *to, socklen_t addrlen);
```

写一个长度为0的数据报是可行的，在导致一个包含IP头部（一般说来，IPv4的头部为20字节）、8字节UDP头部和没有数据的IP数据报。这也意味着对于数据报协议，recvfrom返回0值也是可行的，它不表示对方已关闭了连接，这与TCP套接口上的read返回0的情况不同。

由于UDP是无连接的，这就没有诸如关闭UDP连接之类的事情。

如果recvfrom的参数from是空指针，则相应的长度参数（addrlen）也必须是空指针，这表示我们并不关心发数据方的协议地址。

大多数TCP服务器是并发的，而大多数UDP服务器是迭代的。



#### 什么是socket?

socket 可以看成是用户进程与内核网络协议栈的编程接口。

#### 大端/小端


不同主机有不同的字节序。系统所用的字节序称为主机字节序（host byte order）。
网络字节序规定为大端字节序。

- 小端（little-endian）字节序：低序字节存储在起始地址；

举一个例子，比如数字0x12 34 56 78在内存中的表示形式。
低地址 --------------------> 高地址
0x12  |  0x34  |  0x56  |  0x78


- 大端（big-endian）字节序：高序字节存储在起始地址。

低地址 --------------------> 高地址
0x78  |  0x56  |  0x34  |  0x12

```cpp
// 判断当前系统是大端模式还是小端模式
void main()
{
    unsigned int data = 0x12345678;
    char* p = (char*)&data;

    printf("%x, %x, %x, %x\n", p[0], p[1], p[2], p[3]);
    if (p[0] == 0x78) {
        printf("当前系统是小端模式\n");
    } else {
        printf("当前系统是大端模式\n");
    }
}
```

```c
union {
    int i;
    char c[sizeof(int)];
}u;

u.i = 0x12345678;
if (u.c[0] == 0x78) {
    printf("当前系统是小端模式\n");
}
else {
    printf("当前系统是大端模式\n");
}
```


#### TCP三次握手

发送端首先发送一个带SYN（synchronize）标志的数据包给接收方【第一次的seq序列号是随机产生的，这样是为了网络安全，如果不是随机产生初始序列号，黑客将会以很容易的方式获取到你与其他主机之间的初始化序列号，并且伪造序列号进行攻击】；

接收端收到后，回传一个带有SYN/ACK（acknowledgement）标志的数据包以示传达确认信息【SYN 是为了告诉发送端，发送方到接收方的通道没问题；ACK 用来验证接收方到发送方的通道没问题】；

最后，发送端再回传一个带ACK标志的数据包，代表握手结束，若在握手某个过程中某个阶段莫名中断，TCP协议会再次以相同的顺序发送相同的数据包。


三次握手最主要的目的就是双方确认自己与对方的发送与接收是正常的。

```
第一次握手，发送端：什么都确认不了；接收端：对方发送正常，自己接受正常。
第二次握手，发送端：对方发送&接受正常，自己发送&接受正常 ；接收端：对方发送正常，自己接受正常。
第三次握手，发送端：对方发送&接受正常，自己发送&接受正常；接收端：对方发送&接受正常，自己发送&接受正常。
```

Q：两次握手不行吗？为什么TCP客户端最后还要发送一次确认呢？

主要防止已经失效的连接请求报文突然又传送到了服务器，从而产生错误。

经典场景：客户端发送了第一个请求连接并且没有丢失，只是因为在网络结点中滞留的时间太长了由于TCP的客户端迟迟没有收到确认报文，以为服务器没有收到，此时重新向服务器发送这条报文，此后客户端和服务器经过两次握手完成连接，传输数据，然后关闭连接。

此时此前滞留的那一次请求连接，网络通畅了到达服务器，这个报文本该是失效的，但是，两次握手的机制将会让客户端和服务器再次建立连接，这将导致不必要的错误和资源的浪费。

如果采用的是三次握手，就算是那一次失效的报文传送过来了，服务端接受到了那条失效报文并且回复了确认报文，但是客户端不会再次发出确认。由于服务器收不到确认，就知道客户端并没有请求连接。


Q：TCP三次握手中，最后一次回复丢失，会发生什么？

如果最后一次ACK在网络中丢失，那么Server端（服务端）该TCP连接的状态仍为SYN_RECV，并且根据TCP的超时重传机制依次等待3秒、6秒、12秒后重新发送 SYN+ACK 包，以便 Client（客户端）重新发送ACK包；

如果重发指定次数后，仍然未收到ACK应答，那么一段时间后，Server（服务端）自动关闭这个连接；

但是Client（客户端）认为这个连接已经建立，如果Client（客户端）端向Server（服务端）发送数据，Server端（服务端）将以RST包（Reset，标示复位，用于异常的关闭连接）响应，此时，客户端知道第三次握手失败。

#### TCP四次挥手

主动断开方（客户端/服务端）-发送一个 FIN，用来关闭主动断开方（客户端/服务端）到被动断开方（客户端/服务端）的数据传送；

被动断开方（客户端/服务端）-收到这个 FIN，它发回一个 ACK，确认序号为收到的序号加1 。和 SYN 一样，一个 FIN 将占用一个序号；

被动断开方（客户端/服务端）-关闭与主动断开方（客户端/服务端）的连接，发送一个FIN给主动断开方（客户端/服务端）；

主动断开方（客户端/服务端）-发回 ACK 报文确认，并将确认序号设置为收到序号加1。


Q：为什么连接的时候是三次握手，关闭的时候却是四次握手？

建立连接的时候， 服务器在LISTEN状态下，收到建立连接请求的SYN报文后，把ACK和SYN放在一个报文里发送给客户端。

关闭连接时，服务器收到对方的FIN报文时，仅仅表示对方不再发送数据了但是还能接收数据，而自己也未必全部数据都发送给对方了,所以服务器可以立即关闭，也可以发送一些数据给对方后，再发送FIN报文给对方来表示同意现在关闭连接。因此，服务器ACK和FIN一般都会分开发送，从而导致多了一次。


Q：为什么客户端最后还要等待2MSL？为什么还有个TIME-WAIT的时间等待？

保证客户端发送的最后一个ACK报文能够到达服务器，因为这个ACK报文可能丢失，服务器已经发送了FIN+ACK报文，请求断开，客户端却没有回应，于是服务器又会重新发送一次，而客户端就能在这个2MSL时间段内收到这个重传的报文，接着给出回应报文，并且会重启2MSL计时器。

防止类似与“三次握手”中提到了的“已经失效的连接请求报文段”出现在本连接中。客户端发送完最后一个确认报文后，在这个2MSL时间中，就可以使本连接持续的时间内所产生的所有报文段都从网络中消失，这样新的连接中不会出现旧连接的请求报文。

2MSL，最大报文生存时间，一个MSL 30 秒，2MSL = 60s。只要是主动断开连接的，就会有 TIME_WAIT状态。


Q：TIME-WAIT 状态过多会产生什么后果？怎样处理？

作为服务器，短时间内关闭了大量的Client连接，就会造成服务器上出现大量的TIME_WAIT连接，占据大量的tuple /tApl/ ，严重消耗着服务器的资源，此时部分客户端就会显示连接不上；

作为客户端，短时间内大量的短连接，会大量消耗的Client机器的端口，毕竟端口只有65535个，端口被耗尽了，后续就无法在发起新的连接了。

短连接表示“业务处理+传输数据的时间 远远小于 TIMEWAIT超时的时间”的连接。

解决方法：

用负载均衡来抗这些高并发的短请求；

服务器可以设置 SO_REUSEADDR 套接字选项来避免 TIME_WAIT状态，TIME_WAIT 状态可以通过优化服务器参数得到解决，因为发生TIME_WAIT的情况是服务器自己可控的，要么就是对方连接的异常，要么就是自己没有迅速回收资源，总之不是由于自己程序错误导致的；

强制关闭，发送 RST 包越过TIMEWAIT状态，直接进入CLOSED状态。


Q：服务器出现了大量 CLOSE_WAIT 状态如何解决？

大量 CLOSE_WAIT 表示程序出现了问题，对方的 socket 已经关闭连接，而我方忙于读或写没有及时关闭连接，需要检查代码，特别是释放资源的代码，或者是处理请求的线程配置。


#### 常见错误

- EAGAIN

从字面上来看，是提示再试一次。这个错误经常出现在当应用程序进行一些非阻塞(non-blocking)操作(对文件或socket)的时候。例如，以 O_NONBLOCK的标志打开文件/socket/FIFO，如果你连续做read操作而没有数据可读，此时程序不会阻塞起来等待数据准备就绪返回，read函数会返回一个错误EAGAIN，提示你的应用程序现在没有数据可读请稍后再试。

- EINTR

如果发生EINTR错误(系统调用被一个捕获的信号中断)，则继续读写。

#### 基本套接口编程

##### 套接口地址结构

- IPv4套接口地址结构

```c
// man 7 ip
struct sockaddr_in {
   sa_family_t    sin_family; /* address family: AF_INET */
   in_port_t      sin_port;   /* port in network byte order */
   struct in_addr sin_addr;   /* internet address */
};

/* Internet address. */
struct in_addr {
   uint32_t       s_addr;     /* address in network byte order */
};
```

通用的套接口地址结构：

```c
struct sockaddr {
    uint8_t     sa_len; 
    sa_family_t sa_family;   /* address family */
    char        sa_data[14]; /* protocol-specific address */
}

int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);

struct sockaddr_in serv;
bind(sockfd, (struct sockaddr*)&serv, sizeof(serv));

```

从应用程序开发人员的观点看，这些通用的套接口地址结构的唯一用途是给指向特定于协议的地址结构的指针转换类型。



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

```c
#include <sys/socket.h>
// 返回与套接口关联的本地协议地址
int getsockname(int sockfd, struct sockaddr *addr, socklen_t *addrlen);
// 返回与套接口关联的远程协议地址
int getpeername(int sockfd, struct sockaddr *addr, socklen_t *addrlen);
```


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

##### readn、writen和readline函数

函数readline每读一个字节的数据就要调用一次系统的read函数，其效率是非常低的。



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

执行主动关闭的那一端进入TIME_WAIT状态。

1. 实现终止TCP全双工连接的可靠性

假设最后一个ACK丢失，对方将重发最终的FIN，因此客户端必须维护状态信息以允许它重发最终的ACK。如果不维护状态信息，它将响应以RST，而对方则把它解释成一个错误。如果TCP打算执行所有必要的工作以彻底终止某个连接上两个方向的数据流（即全双工关闭），那么它必须正确处理连接终止序列四个分节中任何一个分节的丢失情况。这也说明了执行主动关闭的一端为何进入TIME_WAIT状态，因为它可能不得不重发最终的ACK。

2. 允许老的重复分节在网络中消逝

假设存在一个TCP连接，我们关闭这个连接后，在以后某个时候又重新建立起相同的IP和Port之间的TCP连接，后一个连接称为前一个连接的化身，因为他们的IP和Port都相同。TCP必须防止来自某个连接的老重复分组在连接终止后再现，从而被误解成属于同一连接的化身。要实现这种功能，TCP不能给处于TIME_WAIT状态的连接启动新的化身。既然TIME_WAIT状态的持续时间是2MLS（最大分节生命期MSL(maximum segment lifetime)的2倍，不同实现，时间长短不同，1min ~ 4min，MSL是IP数据报能在互联网中生存的最长时间），这就足够让某个方向上的分组最多存活MSL秒即被丢弃，另一个方向上的应答最多存活MSL秒也被丢弃。通过实施这个规则，我们能够保证当成功建立一个TCP连接时，来自该连接先前化身的老重复分组都已经在网络中消逝。


#### 缓冲区大小及限制

IPv4数据报的最大大小是65535字节，包括IPv4头部。

最大传输单元MTU(maximum transmission unit)，是指一种通信协议的某一层上面所能通过的最大数据包大小，由硬件规定。
以太网的MTU为1500字节。IPv4要求的最小MTU是68字节。

两台主机间的路径上的最小MTU称为路径MTU（path MTU）。1500字节的以太网MTU是当今常见的路径MTU。

当一个IP数据报从某个接口发出时，如果它的大小超过相应链路的MTU，IPv4将执行分片（fragmentation），各片段到达目的地前不会被重组。


IPv4定义了最小重组缓冲区大小（minimum reassembly buffer size）: 任何IPv4的实现都必须支持的最小数据报大小，对于IPv4其值为576字节。


TCP有一个MSS，最大报文长度，只是在建立连接的时候，告诉对方我最大能够接收多少数据，在数据通信的过程中就没有mss了。SYN分节带有MSS选项，MSS的目的是告诉对方其重组缓冲区大小的实际值，从而避免分片。MSS经常设置成MTU减去IP和TCP头部的固定长度，在以太网中使用IPv4的MSS为1460。可以使用TCP_MAXSEG获取与设置。

窗口规模选项，可以通过SO_RCVBUF进行相关设置。



- TCP输出

每个TCP套接口有一个发送缓冲区，可以用SO_SNDBUF修改这一缓冲区的大小。当应用进程调用write时，内核从应用进程的缓冲区中拷贝所有数据到套接口的发生缓冲区。如果容不下，应用进程将被挂起（睡眠），直到所有数据都拷贝到套接口发送缓冲区，所以从写一个TCP套接口的write调用成功返回仅仅表示我们可以重新使用应用进程的缓冲区，并不告诉我们对方的TCP对对方应用进程已经接受到数据。

对方TCP必须确认收到数据，只有收到对方的ACK，本方TCP才能删除套接口发送缓冲区中已确认的数据，TCP必须保留数据拷贝直到对方确认为止。


- UDP输出

UDP套接口有发送缓冲区大小，可用SO_SNDBUF修改，但是它仅仅是写到套接口的UDP数据报的大小上限。如果应用进程写一大于套接口发送缓冲区（并不存在）的数据报，则返回EMSGSIZE错误。因为UDP是不可靠的，它不必保存应用进程的数据拷贝，因此无需一个真正的发生缓冲区。（应用进程的数据在沿协议栈向下传递时，以某种形式拷贝到内核缓冲区，然而当链路层把数据传出后这个拷贝就丢弃。）


从写UDP套接口的write调用成功地返回表示数据报或所有片段已经被加入链路层的输出队列，如果输出队列没有足够的空间存放数据报或它的某个分节，UDP将返回应用进程ENOBUFS错误。










#### 常见socket选项

- TCP_NODELAY

Nagle算法主要是用来避免大量的小数据包在网络中传输，从而降低网络容量利用率。比如一个20字节的TCP首部+20字节的IP首部+1个字节的数据组成的TCP数据报，有效传输通道利用率只有将近1/40。如果网络充斥着这样的小分组数据，则网络资源的利用率是相当低下的。但是对于一些需要小包场景的程序，比如像telnet或ssh这样的交互性比较强的程序，你需要关闭这个算法。可以在Socket设置TCP_NODELAY选项来关闭这个算法。


##### 函数getsockopt和setsockopt

```c
#include <sys/socket.h>
int getsockopt(int sockfd, int level, int optname, void *optval, socklen_t *optlen);
int setsockopt(int sockfd, int level, int optname, const void *optval, socklen_t *optlen);
```
仅用户套接口。sockfd必须指向一个打开的套接口描述字。


- SO_BROADCAST套接口选项
此选项使能或禁止进程发送广播消息的能力。

- SO_KEEPALIVE套接口选项
给一个TCP套接口设置保持存活（keepalive）选项后，如果2小时内在此套接口的任一方向都没有数据交换，TCP就自动给对方发一个保持存活探测分节（keepalive probe）。

因为要考虑到一个服务器通常会连接多个客户端，因此由用户在应用层自己实现心跳包，代码较多 且稍显复杂，而利用TCP／IP协议层为内置的KeepAlive功能来实现心跳功能则简单得多。 不论是服务端还是客户端，一方开启KeepAlive功能后，就会自动在规定时间内向对方发送心跳包， 而另一方在收到心跳包后就会自动回复，以告诉对方我仍然在线。 因为开启KeepAlive功能需要消耗额外的宽带和流量，所以TCP协议层默认并不开启KeepAlive功 能，尽管这微不足道，但在按流量计费的环境下增加了费用，另一方面，KeepAlive设置不合理时可能会 因为短暂的网络波动而断开健康的TCP连接。并且，默认的KeepAlive超时需要7,200，000 MilliSeconds， 即2小时，探测次数为5次。对于很多服务端应用程序来说，2小时的空闲时间太长。因此，我们需要手工开启KeepAlive功能并设置合理的KeepAlive参数。


- SO_REUSEADDR和SO_REUSEPORT套接口选项

1. SO_REUSEADDR允许启动一个监听服务器并捆绑其众所周知端口，即使以前建立的将此端口用作它们的本地端口的连接仍存在。这个条件通常是这样碰到的：
（a）启动一个监听服务器；
（b）连接请求到达，派生一个子进程来处理这个客户；
（c）监听服务器终止，但子进程继续为现有连接上的客户提供服务；
（d）重启监听服务器。

缺省时，当监听服务器在步骤（d）通过调用socket、bind和listen重启时，因为它试图捆绑一个现有连接上的端口（正由以前派生的子进程所处理），所以调用bind失败。但若服务器在socket和bind的调用间设置SO_REUSEADDR套接口选项，bind将成功。

2. SO_REUSEADDR允许在同一端口上启动同一服务器的多个实例，只要每个实例捆绑一个不同的本地IP地址即可。

3. SO_REUSEADDR允许单个进程捆绑同一端口到多个套接口上，只要每个捆绑指定不同的本地IP地址即可。

4. SO_REUSEADDR允许完全重复的捆绑：当一个IP地址和端口绑定到某个套接口上时，还允许捆绑到另一个套接口上，一般来说，这个特性仅在支持多播的系统上才有。




- SOCK_CLOEXEC    

Set  the  close-on-exec (FD_CLOEXEC) flag on the new file descriptor.  See the description of the O_CLOEXEC flag in open(2) for reasons why this may be useful.


linux进程间的close-on-exec机制：

父进程监听一个端口后，fork出一个子进程，然后kill掉父进程，再重启父进程，这个时候提示端口占用，用netstat查看，子进程占用了父进程监听的端口。

在创建socket的时候加上SOCK_CLOEXEC标志，在fork子进程中执行exec的时候，会清理掉父进程创建的socket。


- SOMAXCONN

每一个处于监听(Listen)状态的端口，都有自己的监听队列。

SOMAXCONN参数定义了系统中每一个端口最大的监听队列的长度, 这是个全局的参数, 默认值为128。

somaxconn内核参数默认值一般是128，对于负载很大的服务程序来说大大的不够。一般会将它修改为2048或者更大。
```sh
echo 2048 > /proc/sys/net/core/somaxconn 
```
但是这样系统重启后保存不了。

在/etc/sysctl.conf中添加如下：
```sh
net.core.somaxconn = 2048
```
然后在终端中执行：
```sh
sysctl -p
```



##### fcntl 函数

fcntl代表“file control(文件控制)”，此函数进行各种描述字控制操作。

- 通过用F_SETFL命令设置O_NONBLOCK文件状态标志，来设置套接口为非阻塞型。
```c
int flags;
// set socket nonblocking
if ((flags = fcntl(fd, F_GETTL, 0)) < 0) {
    err_sys("F_GETFL error");
}
flags |= O_NONBLOCK;
if (fcntl(fd, F_SETFL, flags) < 0) {
    err_sys("F_SETFL error");
}
```

```c
// 关闭非阻塞标志
flags &= ~O_NONBLOCK;
if (fcntl(fd, F_SETFL, flags) < 0) {
    err_sys("F_SETFL error");
}
```

- 通过用F_SETFL命令来设置O_ASYNC文件状态标志，导致在套接口状态发生变化时内核生成信号SIGIO。





## I/O模型

Unix中五个I/O模型的基本区别：

- 阻塞I/O模型
假设函数recvfrom为系统调用，进程调用recvfrom，此系统调用直到数据报到达且拷贝到应用缓冲区或者出错才返回。

- 非阻塞I/O模型
轮询（polling），应用进程连续不断地查询内核，看看某操作是否准备好，这对CPU时间是极大地浪费。

- I/O复用(select和poll)模型
如果一个或多个I/O条件满足（例如：输入已准备好被读），我们就被通知到，这个能力就被称为I/O复用。
调用select或poll，在这两个系统调用的某一个上阻塞，而不是阻塞在真正的I/O系统调用。当select返回套接口可读条件时，我们调用recvfrom将数据拷贝到应用缓冲区中。
使用select的好处在于我们可以等待多个描述字准备好。

- 信号驱动I/O(SIGIO)模型
通过系统调用sigaction安装一个信号处理程序，此系统调用立即返回，进程继续工作，它是非阻塞的，当数据报准备好被读时，就为该进程生成一个SIGIO信号，我们随即可以在信号处理程序中调用recvfrom来读数据报。

- 异步I/O模型
让内核启动操作，并在整个操作完成后通知我们。与信号驱动I/O的区别：信号驱动I/O是内核通知我们何时可以启动一个I/O操作；而异步I/O是由内核通知我们I/O操作合适完成。


## select与epoll

首先我们来定义流的概念，一个流可以是文件，socket，pipe等等可以进行I/O操作的内核对象。不管是文件，还是套接字，还是管道，我们都可以把他们看作流。之后我们来讨论I/O的操作，通过read，我们可以从流中读入数据；通过write，我们可以往流写入数据。现在假定一个情形，我们需要从流中读数据，但是流中还没有数据，（典型的例子为，客户端要从socket读如数据，但是服务器还没有把数据传回来），这时候该怎么办？

- 阻塞

阻塞是个什么概念呢？比如某个时候你在等快递，但是你不知道快递什么时候过来，而且你没有别的事可以干（或者说接下来的事要等快递来了才能做）；那么你可以去睡觉了，因为你知道快递把货送来时一定会给你打个电话（假定一定能叫醒你）。

- 非阻塞忙轮询

接着上面等快递的例子，如果用忙轮询的方法，那么你需要知道快递员的手机号，然后每分钟给他挂个电话：“你到了没？”。


为了了解阻塞是如何进行的，我们来讨论缓冲区，以及内核缓冲区，最终把I/O事件解释清楚。缓冲区的引入是为了减少频繁I/O操作而引起频繁的系统调用（你知道它很慢的），当你操作一个流时，更多的是以缓冲区为单位进行操作，这是相对于用户空间而言。对于内核来说，也需要缓冲区。假设有一个管道，进程A为管道的写入方，Ｂ为管道的读出方。

假设一开始内核缓冲区是空的，B作为读出方，被阻塞着。然后首先A往管道写入，这时候内核缓冲区由空的状态变到非空状态，内核就会产生一个事件告诉Ｂ该醒来了，这个事件姑且称之为“缓冲区非空”。但是“缓冲区非空”事件通知B后，B却还没有读出数据；且内核许诺了不能把写入管道中的数据丢掉这个时候，Ａ写入的数据会滞留在内核缓冲区中，如果内核也缓冲区满了，B仍未开始读数据，最终内核缓冲区会被填满，这个时候会产生一个I/O事件，告诉进程A，你该等等（阻塞）了，我们把这个事件定义为“缓冲区满”。假设后来Ｂ终于开始读数据了，于是内核的缓冲区空了出来，这时候内核会告诉A，内核缓冲区有空位了，你可以从长眠中醒来了，继续写数据了，我们把这个事件叫做“缓冲区非满”也许事件已经通知了A，但是A也没有数据写入了，而Ｂ继续读出数据，知道内核缓冲区空了。这个时候内核就告诉B，你需要阻塞了，我们把这个时间定为“缓冲区空”。
 
这四个情形涵盖了四个I/O事件，缓冲区满，缓冲区空，缓冲区非空，缓冲区非满（注都是说的内核缓冲区）。这四个I/O事件是进行阻塞同步的根本。


- 阻塞I/O的缺点

阻塞I/O模式下，一个线程只能处理一个流的I/O事件。如果想要同时处理多个流，要么多进程(fork)，要么多线程(pthread_create)，很不幸这两种方法效率都不高。

于是再来考虑非阻塞忙轮询的I/O方式，我们发现我们可以同时处理多个流了

```cpp
while true {
    for i in stream[]; {
        if i has data
            read until unavailable
    }
}
```

只要不停的把所有流从头到尾问一遍，又从头开始。这样就可以处理多个流了，但这样的做法显然不好，因为如果所有的流都没有数据，那么只会白白浪费CPU。这里要补充一点，阻塞模式下，内核对于I/O事件的处理是阻塞或者唤醒，而非阻塞模式下则把I/O事件交给其他对象（后文介绍的select以及epoll）处理甚至直接忽略。

为了避免CPU空转，可以引进了一个代理（一开始有一位叫做select的代理，后来又有一位叫做poll的代理，不过两者的本质是一样的）。这个代理比较厉害，可以同时观察许多流的I/O事件，在空闲的时候，会把当前线程阻塞掉，当有一个或多个流有I/O事件时，就从阻塞态中醒来，于是我们的程序就会轮询一遍所有的流（于是我们可以把“忙”字去掉了）。代码长这样:

```cpp
while true {
    select(streams[])
    for i in streams[] {
        if i has data
            read until unavailable
    }
}
```

于是，如果没有I/O事件产生，我们的程序就会阻塞在select处。但是依然有个问题，我们从select那里仅仅知道了，有I/O事件发生了，但却并不知道是那几个流（可能有一个，多个，甚至全部），我们只能无差别轮询所有流，找出能读出数据，或者写入数据的流，对他们进行操作。但是使用select，我们有O(n)的无差别轮询复杂度，同时处理的流越多，每一次无差别轮询时间就越长。


说了这么多，终于能好好解释epoll了epoll可以理解为event poll，不同于忙轮询和无差别轮询，epoll之会把哪个流发生了怎样的I/O事件通知我们。此时我们对这些流的操作都是有意义的。一个epoll模式的代码大概的样子是：

```cpp
while true {
    active_stream[] = epoll_wait(epollfd)
    for i in active_stream[] {
        read or write till unavailable
    }
}
```

### select与epoll的区别

- epoll和select都是I/O多路复用的技术，都可以实现同时监听多个I/O事件的状态。
- epoll相比select效率更高，主要是基于其操作系统支持的I/O事件通知机制，而select是基于轮询机制。


#### select函数

该函数允许进程指示内核等待多个事件中的任意一个发生，并仅在一个或多个事件发生或经过某个指定的时间后才唤醒进程。
```c
#include <sys/time.h>
/* maxfdp1: 比如3个文件描述符：1、3、7，则maxfdp1 = max(1、3、7) + 1 = 7 + 1 = 8*/
int select(int maxfdp1, fd_set *readset, fd_set *writeset, fd_set *exceptset, const struct timeval *timeout);

struct timeval {
    long tv_sec;  // seconds
    long tv_usec; // microseconds
};
```

有三种可能：
1. 永远等待下去，仅在有一个描述字准备好I/O时返回，为此，将参数timeout设为空指针;
2. 等待固定时间，在有一个描述字装备好I/O时返回，但不超过由timeout参数中的秒数和微秒数；
3. 根本不等待，检查描述字后立即返回，这称为轮询（polling），参数timeout必须指向结构timeval，并且定时器的值为0。


```c
// 从集合内把一个描述符移除		  
void FD_CLR(int fd, fd_set *set);
// 测试某个描述符是否在集合内
int  FD_ISSET(int fd, fd_set *set);
// 把一个描述符加入集合
void FD_SET(int fd, fd_set *set);
// 清空集合
void FD_ZERO(fd_set *set);
```


函数select中的三个中间参数readset、writeset、exceptset可以设为空指针，如果都为空指针，我们就有了一个比Unix的函数sleep更为精确地定时器(sleep函数的最小单位为秒)。


参数maxfdp1的值为（最大描述字+1），例如：打开描述字1、4和5，则maxfdp1 = 6。

头文件<sys/select.h>中定义的常量FD_SETSIZE，是数据类型fd_set的描述字数量，其值通常为1024。


#### select的用法

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>

int
main(void)
{
   fd_set rfds;
   struct timeval tv;
   int retval;

   /* Watch stdin (fd 0) to see when it has input. */
   FD_ZERO(&rfds);
   FD_SET(0, &rfds);

   /* Wait up to five seconds. */
   tv.tv_sec = 5;
   tv.tv_usec = 0;

   /* retval：发生变化的文件描述符的个数 */
   retval = select(1, &rfds, NULL, NULL, &tv); 
   /* Don't rely on the value of tv now! */

   if (retval == -1)
       perror("select()");
   else if (retval) {
       printf("Data is available now.\n");
       FD_ISSET(0, &rfds); // 检测0号文件描述符是否在集合中，rfds既做输入又做输出
   }
   else
       printf("No data within five seconds.\n");

   exit(EXIT_SUCCESS);
}
```

```c
/**
 * read_timeout: 读超时检测函数，不含读操作
 * @fd: 文件描述符
 * @wait_seconds: 等待超时秒数，如果为0表示不检测超时
 * 成功（未超时）返回0，失败返回-1,超时返回-1并且error==ETIMEDOUT
 */
int read_timeout(int fd, unsigned int wait_seconds)
{
    int ret = 0;
    if (wait_seconds > 0) {
        fd_set read_fdset;
        struct timeval timeout;

        FD_ZERO(&read_fdset);
        FD_SET(fd, &read_fdset);

        timeout.tv_sec = wait_seconds;
        timeout.tv_usec = 0;

        // select返回值三态：
        // 1. 若timeout时间到(超时)，没有检测到读事件，ret返回0；
        // 2. 若(ret < 0) && (errno == EINTR) 说明select的过程中被别的信号中断(可中断睡眠原理)；若返回-1，select出错；
        // 3. 若返回值ret > 0, 表示有read事件发生，返回事件发生的个数。

        do {
            ret = select(fd+1, &read_fdset, NULL, NULL, &timeout);
        } while(ret < 0 && errno == EINTR);

        if (ret == 0) {
            ret = -1;
            errno = ETIMEDOUT;
        }
        else if (ret == 1) {
            ret = 0; // socket API中成功一般都返回0
        }
    }

    return ret;
}
```


```c
// 使用read_timeout函数
// ... 
while(1) {
    memset(recvbuf, 0, sizeof(recvbuf));
    ret = read_timeout(conn, wait_seconds);
    if (ret == 0) {
        datalen = readline(conn, recvbuf, sizeof(recvbuf));
        if (datalen < 0) {
            ERR_EXIT("read func err\n");
        }
        if (datalen == 0) {
            printf("客户端已经关闭");
            close(conn);
            ERR_EXIT("readline return 0\n");
        }
    }
    else if (ret == -1 && errno == ETIMEDOUT) {
        printf("读超时\n");
        continue;
    }
    else if (ret < 0) {
        ERR_EXIT("select Failed\n");
    }
    fputs(recvbuf, stdout);
}
```




#### 描述字在什么条件下准备好？

1. 满足任一条件时，准备好读：

- 套接口接收缓冲区的数据字节数 > 接收缓冲区低潮限度的当前值(可以通过SO_RCVLOWAT，对于TCP和UDP默认为1)，读操作不阻塞且返回值 > 0；

- 接收到了FIN的TCP连接，读操作不阻塞且返回0（即文件结束符）；

- 套接口是一个监听套接口，且已经完成的连接数非0；

- 有一个套接口错误待处理，读操作不阻塞且返回一个错误（-1），errno则含有明确的错误码。

2. 满足任一条件时，准备好写：

- 套接口发送缓冲区中的可用空间字节数 > 发送缓冲区低潮限度的当前值（可通过SO_SNDLOWAT设置），并且（套接口已连接 或 套接口不要求连接），写操作不阻塞且返回一个正值；

- 连接的写这一半关闭。此时写操作将产生信号SIGPIPE；

- 有一个套接口错误待处理，此时写操作将不阻塞且返回一个错误（-1），errno则含有明确的错误码。。


注意：当一个套接口出错时，它由select标记为既可读又可写。

设置低潮限度的目的：如果我们知道除非至少有64字节的数据可用，否则我们的应用进程不能完成有效的工作，那么就可以将接收低潮限度设置为64，以防止小于64个字节的数据准备好读时select就唤醒我们。


select处理的各种条件：
1. 如果对方TCP发送数据，套接口变为可读且read返回大于0的值（即数据的字节数）；
2. 如果对方TCP发送一个FIN（对方进程终止），套接口变为可读且read返回0（文件结束）；
3. 如果对方TCP发送一个RST（对方主机崩溃并重启），套接口变为可读且read返回-1，errno则含有明确的错误码。



#### poll函数
poll提供了与select相似的功能。
```c
#include <poll.h>
int poll(struct pollfd *fdarray, unsigned long nfds, int timeout);

struct pollfd {
    int fd;  // descriptor to check
    short events;  // events of interest on fd
    short revents; // events that occurred on fd
};
```
因为分配一个结构pollfd的数组并通知内核数组中元素的数目是调用者的责任，所以这里没有类似于内核知道的fd_set之类固定大小的数据类型。

poll的机制与select类似，与select在本质上没有多大差别，也是对管理的多个描述符进行轮询，根据描述符的状态进行处理，但是poll没有最大文件描述符数量的限制。

poll和select同样存在的一个缺点：包含大量文件描述符的数组被整体复制于用户态和内核态的地址空间之间，而不论这些描述符是否就绪，开销随着文件描述符数量的增加而线性增大。



#### epoll

相比于select，epoll最大的好处在于它不会随着监听fd数目的增长而降低效率。

- 创建epoll句柄
```c
int epfd = epoll_create(int size);
```
创建一个epoll句柄, size用来告诉内核这个监听的数目一共有多大。这个参数不同于select()中的第一个参数（fd+1）。需要注意的是，当创建好epoll句柄后，它就是会占用一个fd值，在linux下如果查看/proc/进程id/fd/，是能够看到这个fd的，所以在使用完epoll后，必须调用close()关闭，否则可能导致fd被耗尽。

该函数生成一个epoll专用的文件描述符。它其实是在内核申请一空间，用来存放你想关注的socket fd上是否发生以及发生了什么事件。size就是你在这个epoll fd上能关注的最大socket fd数。


- 将被监听的描述符添加到epoll句柄，或从epoll句柄中删除，对监听事件进行修改。
```c
int epoll_ctl(int epfd, int op, int fd, struct epoll_event* event);
```
该函数用于控制某个epoll文件描述符上的事件，可以注册事件、修改事件、删除事件。

参数：
```sh
epfd: 由epoll_create生成的epoll专用文件描述符；
op: 要进行的操作

 - EPOLL_CTL_ADD: 注册新的fd到epfd中;
 - EPOLL_CTL_MOD修改已经注册的fd的监听事件;
 - EPOLL_CTL_DEL从epfd中删除一个fd.
    
fd: 需要监听的fd；
event: 告诉内核需要监听什么事件。
```

struct epoll_event的结构如下：
```h
typedef union epoll_data {
    void* ptr;
    int fd;
    __uint32_t u32;
    __uint64_t u64;
} epoll_data_t;

struct epoll_event {
    __uint32_t events;
    epoll_data_t data;
};
```

events可以是以下几个宏的集合：

```sh
EPOLLIN: 触发该事件，表示对应的文件描述符上有可读数据（包括对端socket正常关闭）；

EPOLLOUT: 触发该事件，表示对应的文件描述符上可以写数据；

EPOLLPRI: 表示对应的文件描述符有紧急的数据可读（这里应该表示有带外数据到来）；

EPOLLERR: 表示对应的文件描述符发生错误；

EPOLLHUP: 表示对应的文件描述符被挂断；

EPOLLET: 将EPOLL设为边缘触发(Edge Triggered) 模式，这是相对于水平触发(Level Triggered)来说的；

EPOLLONESHOT: 只监听一次事件，当监听完这次事件之后，如果还需要继续监听这个socket，则需要再次把这个socket加入到EPOLL队列中。

```

例如：
```c
struct epoll_event ev;
// 设置与要处理的事件相关的文件描述符
ev.data.fd = listenfd;
// 设置要处理的事件类型
ev.events = EPOLLIN | EPOLLET;
// 注册epoll事件
epoll_ctl(epfd, EPOLL_CTL_ADD, lsitenfd, &ev);
```

- 等待事件触发，当超过timeout还没有事件触发时，就超时。
```h
int epoll_wait(int epfd, struct epoll_event* events, int maxevents, int timeout);
```
等待事件的产生，类似于select()调用。参数events用来从内核得到事件的集合，maxevents告诉内核这个events有多大（数组成员的个数），这个maxevents的值不能大于创建epoll_create()时的size。


epoll_wait运行的原理：等待注册在epfd上的socket fd的事件的发生，如果发生则将发生的socket fd和事件类型放入到events数组中，并将注册在epfd上的socket fd的事件类型清空，如果下一个循环还需要关注这个socket fd，则需要用epoll_ctl(epfd, EPOLL_CTL_MOD, listenfd, &ev)来重新设置socket fd的事件类型, 这时不要EPOLL_CTL_ADD，因为socket fd并未清空，只是事件类型清空。


- Edge Triggered(ET)和Level Triggered(LT)

Edge Triggered(ET)：高速工作方式，错误率比较大，只支持no_block socket；

Level Triggered(LT)：默认工作方式，错误率较小，支持block socket和no_blocksocket。

LT(level triggered)是epoll缺省的工作方式，并且同时支持block和no-block socket. 在这种做法中，内核告诉你一个文件描述符是否就绪了，然后你可以对这个就绪的fd进行IO操作。如果你不作任何操作，内核还是会继续通知你的，所以，这种模式编程出错误可能性要小一点。传统的select/poll都是这种模型的代表。

ET (edge-triggered)是高速工作方式，只支持no-block socket，它效率要比LT更高。ET与LT的区别在于，当一个新的事件到来时，ET模式下当然可以从epoll_wait调用中获取到这个事件，可是如果这次没有把这个事件对应的套接字缓冲区处理完，在这个套接字中没有新的事件再次到来时，在ET模式下是无法再次从epoll_wait调用中获取这个事件的。而LT模式正好相反，只要一个事件对应的套接字缓冲区还有数据，就总能从epoll_wait中获取这个事件。



本质上，select, poll, epoll都是同步IO, 因为都需要在读写事件就绪后自己负责进行读写，也就是说这个读写过程是阻塞的。而异步IO无需自己进行读写，异步IO会负责把数据从内核拷贝到用户空间。


## HTTP

### Web应用的本质

了解了HTTP协议和HTML文档，我们其实就明白了一个Web应用的本质就是：
```
浏览器发送一个HTTP请求；
服务器收到请求，生成一个HTML文档；
服务器把HTML文档作为HTTP响应的Body发送给浏览器；
浏览器收到HTTP响应，从HTTP Body取出HTML文档并显示。
```

## 基本概念

- RTT: 客户端到服务器往返所花时间RTT（round-trip time）。

- 流量控制

TCP提供流量控制。TCP总是告诉对方它能够接收多少字节的数据，这称为通告窗口（advertisted window），任何时刻，这个窗口指出缓冲区中的可用空间，从而确保发送方发送的数据不会溢出接收缓冲区。

窗口时刻动态地变化：当接收方接收发送方的数据时，窗口大小减小，而当接收方应用进程从缓冲区中读取数据时，窗口大小增大。



## 工具

### Wireshark

- 过滤

```c
// IP过滤
ip.addr == x.x.x.x
ip.src == x.x.x.x
ip.dst == x.x.x.x

// 协议过滤
HTTP、ARP...

// 端口过滤
tcp.port == 21
udp.port == 53
tcp.dstport==80 
tcp.srcport==80
udp.srcport==4010
udp.dstport==4010

// 组合过滤
ip.addr == x.x.x.x && tcp.port == 21 && data contains 01:02:03:04:05:06
tcp.port == 21 or udp.port == 53
````



