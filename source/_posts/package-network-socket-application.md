---
title: package-network-socket-application
date: 2020-12-14 23:08:50
tags:
---

## 数据从网卡到应用的过程

<!-- More -->

### 这节是HTTP请求的数据到达网卡，那数据是如何被层层处理并到达应用

![](https://img-blog.csdnimg.cn/20200917100235995.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0pNVzE0MDc=,size_16,color_FFFFFF,t_70#pic_center)

1. 网卡(Network Adapter)，也称网络适配器，是一个 硬件设备，有全球唯一的 MAC(Media Access Control)地址，MAC地址在网卡生产时就被烧制在ROM中，网卡初始化时恢复到计算机中。

![](https://img-blog.csdnimg.cn/20200917100525193.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0pNVzE0MDc=,size_16,color_FFFFFF,t_70#pic_center)

2. 根据 FCS(帧校验序列，Frame Check Sequence)校验数据，判断数据在传输过程是否因噪音等影响导致信号失真，从而导致数据错误，需要丢弃这种无效的数据包

3. 然后 检查 数据包中MAC头部中的接收方的MAC地址，若不是发给自己，则丢弃数据包；若数据包是发给自己，则将数字信息保存到网卡内部缓冲区。

4. 网卡驱动
硬件需要驱动程序来控制，就像电脑需要操作系统一样，而网卡驱动就是CPU控制和使用网卡的程序。

网卡处理完数字信号后，接下来的数据接收需要CPU参与，此时网卡通过中断将数据包达到的事件通知给CPU。接着，CPU暂停手头工作，开始用网卡驱动来干活。

从网卡缓冲区读取接收到的数据
根据MAC头部的以太类型字段判断协议种类并调用处理该协议的软件(即协议栈)
通常我们接触的以太类型是 IP协议，因此会调用TCP/IP协议栈来处理。

- 网卡驱动程序提取这个帧的全部内容，去掉以太网的帧头，然后向上传递给IP层
- IP层接收到包已经，继续去掉IP头的内容，然后交给TCP层
- TCP层根据TCP协议定义的格式，继续解包，read()从socket buffer读取数据，然后传递给应用层
- 应用层根据TCP层传来的数据，按照对应的应用层来分析包


### 总结一下：

1. 网卡收到数据包。

2. 将数据包从网卡硬件缓存转移到服务器内存中。

3. 通知内核处理。

4. 经过TCP/IP协议逐层处理。

5. 应用程序通过read()从socket buffer读取数据。

网卡收到数据包，DMA到内核内存，中断通知内核数据有了，内核按轮次处理消耗数据包，一轮处理完成后，开启硬中断。其核心就是网卡和内核其实是生产和消费模型，网卡生产，内核负责消费，生产者需要通知消费者消费；如果生产过快会产生丢包，如果消费过慢也会产生问题。也就说在高流量压力情况下，只有生产消费优化后，消费能力够快，此生产消费关系才可以正常维持，所以如果物理接口有丢包计数时候，未必是网卡存在问题，也可能是内核消费的太慢。
