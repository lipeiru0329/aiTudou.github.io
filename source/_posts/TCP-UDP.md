---
title: TCP-UDP
date: 2020-07-01 23:56:01
tags:
---

## TCP And UDP

<!-- More -->

## TCP:

![](https://img-blog.csdn.net/20170725204024254?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VuZHlfa2VlcGluZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

- 优点:能够保证数据传输是完整的
- 缺点:由于每次都需要传输确认信息,导致传输效率降低
- 场景:多用于必须保证数据完整性的场景,例如文本信息,支付信息等

### TCP三次握手过程

1. 主机A通过向主机B 发送一个含有同步序列号的标志位的数据段给主机B ,向主机B 请求建立连接,通过这个数据段,
主机A告诉主机B 两件事:我想要和你通信;你可以用哪个序列号作为起始数据段来回应我.
2. 主机B 收到主机A的请求后,用一个带有确认应答(ACK)和同步序列号(SYN)标志位的数据段响应主机A,也告诉主机A两件事:
我已经收到你的请求了,你可以传输数据了;你要用哪佧序列号作为起始数据段来回应我
3. 主机A收到这个数据段后,再发送一个确认应答,确认已收到主机B 的数据段:"我已收到回复,我现在要开始传输实际数据了

这样3次握手就完成了,主机A和主机B 就可以传输数据了.

3次握手的特点:
- 没有应用层的数据 (第三次的可以加上应用程数据)
- SYN这个标志位只有在TCP建产连接时才会被置1
- 握手完成后SYN标志位被置0

### TCP 而断开连接要进行4次

1. 当主机A完成数据传输后,将控制位FIN置1,提出停止TCP连接的请求
2.  主机B收到FIN后对其作出响应,确认这一方向上的TCP连接将关闭,将ACK置1
3. 由B 端再提出反方向的关闭请求,将FIN置1
4. 主机A对主机B的请求进行确认,将ACK置1,双方向的关闭结束.

### TCP客户端最后还要发送一次确认

>一句话，主要防止已经失效的连接请求报文突然又传送到了服务器，从而产生错误。

>如果使用的是两次握手建立连接，假设有这样一种场景，客户端发送了第一个请求连接并且没有丢失，只是因为在网络结点中滞留的时间太长了，由于TCP的客户端迟迟没有收到确认报文，以为服务器没有收到，此时重新向服务器发送这条报文，此后客户端和服务器经过两次握手完成连接，传输数据，然后关闭连接。此时此前滞留的那一次请求连接，网络通畅了到达了服务器，这个报文本该是失效的，但是，两次握手的机制将会让客户端和服务器再次建立连接，这将导致不必要的错误和资源的浪费。

>如果采用的是三次握手，就算是那一次失效的报文传送过来了，服务端接受到了那条失效报文并且回复了确认报文，但是客户端不会再次发出确认。由于服务器收不到确认，就知道客户端并没有请求连接。



由TCP的三次握手和四次断开可以看出,TCP使用面向连接的通信方式,大大提高了数据通信的可靠性,使发送数据端
和接收端在数据正式传输前就有了交互,为数据正式传输打下了可靠的基础

FIN:  发送端完成发送任务位,当TCP完成数据传输需要断开时,提出断开连接的一方将这位置1

### TIME_WAIT状态所带来的影响：

当某个连接的一端处于TIME_WAIT状态时，该连接将不能再被使用。事实上，对于我们比较有现实意义的是，这个端口将不能再被使用。某个端口处于TIME_WAIT状态(其实应该是这个连接)时，这意味着这个TCP连接并没有断开(完全断开)，那么，如果你bind这个端口，就会失败。对于服务器而言，如果服务器突然crash掉了，那么它将无法再2MSL内重新启动，因为bind会失败。解决这个问题的一个方法就是设置socket的SO_REUSEADDR选项。这个选项意味着你可以重用一个地址。

### 如果已经建立了连接，但是客户端突然出现故障了怎么办

>TCP还设有一个保活计时器，显然，客户端如果出现故障，服务器不能一直等下去，白白浪费资源。服务器每收到一次客户端的请求后都会重新复位这个计时器，时间通常是设置为2小时，若两小时还没有收到客户端的任何数据，服务器就会发送一个探测报文段，以后每隔75分钟发送一次。若一连发送10个探测报文仍然没反应，服务器就认为客户端出了故障，接着就关闭连接。


## UDP（User Data Protocol，用户数据报协议）

1. UDP是一个非连接的协议，传输数据之前源端和终端不建立连接，当它想传送时就简单地去抓取来自应用程序的数据，并尽可能快地把它扔到网络上。在发送端，UDP传送数据的速度仅仅是受应用程序生成数据的速度、计算机的能力和传输带宽的限制；在接收端，UDP把每个消息段放在队列中，应用程序每次从队列中读一个消息段。

2. 由于传输数据不建立连接，因此也就不需要维护连接状态，包括收发状态等，因此一台服务机可同时向多个客户机传输相同的消息。

3. UDP信息包的标题很短，只有8个字节，相对于TCP的20个字节信息包的额外开销很小。

4. 吞吐量不受拥挤控制算法的调节，只受应用软件生成数据的速率、传输带宽、源端和终端主机性能的限制。

5. UDP使用尽最大努力交付，即不保证可靠交付，因此主机不需要维持复杂的链接状态表（这里面有许多参数）。

6. UDP是面向报文的。发送方的UDP对应用程序交下来的报文，在添加首部后就向下交付给IP层。既不拆分，也不合并，而是保留这些报文的边界，因此，应用程序需要选择合适的报文大小。

## 小结TCP与UDP的区别：

1. 基于连接与无连接； 
2. 对系统资源的要求（TCP较多，UDP少）； 
3. UDP程序结构较简单； 
4. 流模式与数据报模式 ；
5. TCP保证数据正确性，UDP可能丢包，TCP保证数据顺序，UDP不保证

## DDOS

DOS攻击利用合理的服务请求占用过多的服务资源，使正常用户的请求无法得到相应。

常见的DOS攻击有计算机网络带宽攻击和连通性攻击。

- 带宽攻击指以极大的通信量冲击网络，使得所有可用网络资源都被消耗殆尽，最后导致合法的用户请求无法通过。

- 连通性攻击指用大量的连接请求冲击计算机，使得所有可用的操作系统资源都被消耗殆尽，最终计算机无法再处理合法用户的请求。

- SYN洪水攻击

    SYN洪水攻击属于DOS攻击的一种，它利用TCP协议缺陷，通过发送大量的半连接请求，耗费CPU和内存资源。

    客户端在短时间内伪造大量不存在的IP地址，向服务器不断地发送SYN报文，服务器回复ACK确认报文，并等待客户的确认，由于源地址是不存在的，服务器需要不断的重发直至超时，这些伪造的SYN报文被丢弃，目标系统运行缓慢，严重者引起网络堵塞甚至系统瘫痪。
