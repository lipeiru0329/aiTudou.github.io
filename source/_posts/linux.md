---
title: linux
date: 2021-01-02 20:26:53
tags:
---


## OS 面试

1.  进程的通信方式有哪些

主要分为：管道、系统IPC（包括消息队列、信号量、共享存储）、SOCKET

管道主要分为：普通管道PIPE 、流管道（s_pipe）、命名管道（name_pipe）

- 管道是一种半双工的通信方式，数据只能单项流动，并且只能在具有亲缘关系的进程间流动，进程的亲缘关系通常是父子进程
- 命名管道也是半双工的通信方式，它允许无亲缘关系的进程间进行通信
- 信号量是一个计数器，用来控制多个进程对资源的访问，它通常作为一种锁机制。
- 消息队列是消息的链表，存放在内核中并由消息队列标识符标识。
- 信号是一种比较复杂的通信方式，用于通知接收进程某个事件已经发生。
- 共享内存就是映射一段能被其它进程访问的内存，这段共享内存由一个进程创建，但是多个进程可以访问。

2. 预防死锁：

- 资源一次性分配：（破坏请求和保持条件）

- 可剥夺资源：即当某进程新的资源未满足时，释放已占有的资源（破坏不可剥夺条件）

- 资源有序分配法：系统给每类资源赋予一个编号，每一个进程按编号递增的顺序请求资源，释放则相反（破坏环路等待条件）

3. Banker‘s Algorithm: https://www.coursera.org/lecture/os-pku/yin-xing-jia-suan-fa-7wz4i

    https://www.jianshu.com/p/a47c45cf49c7


    条件： 

        - 有限进程 共享支援
        - 先指定  每个进程 最大的支援
        - 进程需要归还支援
        - 进程在有限时间完成

--> 系统在资源使用率下降的时候：

- 撤销所有的死锁进程
- rollback
- 逐步撤销死锁进程
- 逐步撤销抢占的资源

4. 线程同步的方式

- 互斥量 Synchronized/Lock：采用互斥对象机制，只有拥有互斥对象的线程才有访问公共资源的权限。因为互斥对象只有一个，所以可以保证公共资源不会被多个线程同时访问

- 信号量 Semphare：它允许同一时刻多个线程访问同一资源，但是需要控制同一时刻访问此资源的最大线程数量

- 事件(信号)，Wait/Notify：通过通知操作的方式来保持多线程同步，还可以方便的实现多线程优先级的比较操作

5. 内存管理

- 段式存储管理是一种符合用户视角的内存分配管理方案。在段式存储管理中，将程序的地址空间划分为若干段（segment），如代码段，数据段，堆栈段；这样每个进程有一个二维地址空间，相互独立，互不干扰。段式管理的优点是：没有内碎片（因为段大小可变，改变段大小来消除内碎片）。但段换入换出时，会产生外碎片（比如4k的段换5k的段，会产生1k的外碎片）

- 页式存储管理方案是一种用户视角内存与物理内存相分离的内存分配管理方案。在页式存储管理中，将程序的逻辑地址划分为固定大小的页（page），而物理内存划分为同样大小的帧，程序加载时，可以将任意一页放入内存中任意一个帧，这些帧不必连续，从而实现了离散分离。页式存储管理的优点是：没有外碎片（因为页的大小固定），但会产生内碎片（一个页可能填充不满）。

两者的不同点：

- 目的不同：分页是由于系统管理的需要而不是用户的需要，它是信息的物理单位；分段的目的是为了能更好地满足用户的需要，它是信息的逻辑单位，它含有一组其意义相对完整的信息；

- 大小不同：页的大小固定且由系统决定，而段的长度却不固定，由其所完成的功能决定；

- 地址空间不同： 段向用户提供二维地址空间；页向用户提供的是一维地址空间；

- 信息共享：段是信息的逻辑单位，便于存储保护和信息的共享，页的保护和共享受到限制；

- 内存碎片：页式存储管理的优点是没有外碎片（因为页的大小固定），但会产生内碎片（一个页可能填充不满）；而段式管理的优点是没有内碎片（因为段大小可变，改变段大小来消除内碎片）。但段换入换出时，会产生外碎片（比如4k的段换5k的段，会产生1k的外碎片）。

Reference:
1. https://xie.infoq.cn/article/8423f8eb9ebdcb4b494b81290
2. https://blog.nowcoder.net/n/49211c67aaaa49eb8842f7e979c79498
3. https://blog.csdn.net/justloveyou_/article/details/78304294

---

1、用top命令指定固定的PID
```
top -p 10997
```

查询指定进程的PID
```
ps -ef | grep zookeeper

```

使用ps查询指定进程名或者PID的占用情况
```
ps -aux | grep [name]/[pid]
```

---

一致性：

1. 强一致性（Strong Consistency）
在任何时刻所有的用户或者进程查询到的都是最近一次成功更新的数据。强一致性是程度最高一致性要求，也是最难实现的。关系型数据库更新操作就是这个案例。

2. 单调一致性（Monotonic Consistency）
单调一致性会从读写两个角度有各自的定义。

- 单调读一致性
如果进程已经看到过数据对象的某个值，那么任何后续访问都不会返回该值之前的值。（“If a process has seen a particular value for the object any subsequent accesses will never return any previous values”）

- 单调写一致性
系统保证来自同一个进程的写操作顺序执行。（Write operations that must precede other writes are executed before those other writes.）

3. 最终一致性（Eventual Consistency）
和强一致性相对，在某一时刻用户或者进程查询到的数据可能都不同，但是最终成功更新的数据都会被所有用户或者进程查询到。当前主流的nosql数据库都是采用这种一致性策略。