---
title: golang-goroutine
date: 2020-06-05 22:10:52
tags:
disqusId: aitudou-blog
---

## Goroutine
<!-- More -->

对于一个goroutine来说，虽然指令会被编译器乱序重排，但它其中变量的读, 写操作执行表现必须和从所写的代码得出的预期是一致的。但是在两个不同的goroutine对相同变量操作时, 可能因为指令重排导致不同的goroutine对变量的操作顺序的认识变得不一致。
<!-- More -->
为了解决这种二义性问题，Go语言中引进一个happens before的概念，它用于描述对内存操作的先后顺序问题。如果事件e1 happens before 事件 e2，事件e2 happens after e1。如果，事件e1 does not happen before 事件 e2，并且 does not happen after e2，事件e1和e2同时发生。 -->

对于一个单一的goroutine，happens before 的顺序和代码的顺序是一致的。

为了保证读事件r可以感知对变量v的写事件，我们首先要确保w是变量v的唯一的写事件。同时还要满足以下条件：

“写事件w” happens before “读事件r”。
其他对变量v的访问必须 happens before “写事件w” 或者 happens after “读事件r”。
第二组条件比第一组条件更加严格。因为，它要求在w和 r并行执行的程序中不能再有其他的读操作。对于在单一的goroutine中两组条件是等价的，读事件可以确保感知到对变量的写事件。但是，对于在 两个goroutines共享变量v，我们必须通过同步事件来保证 happens-before 条件 （这是读事件感知写事件的必要条件）。

```
var a string;

func f() {
        print(a);
}

func hello() {
        a = "hello, world";
        go f();
}
```

调用hello函数，会在某个时刻打印“hello, world”（有可能是在hello函数返回之后）。

## Channel communication 管道通信

管道上的发送操作发生在管道的接收完成之前（happens before）。

```
var c = make(chan int, 10)
var a string

func f() {
        a = "hello, world";
        c <- 0;
}

func main() {
        go f();
        <-c;
        print(a);
}
```

可以确保会输出"hello, world"。因为，a的赋值发生在向管道 c发送数据之前，而管道的发送操作在管道接收完成之前发生。因此，在print 的时候，a已经被赋值。

## 锁 Lock

包sync实现了两种类型的锁： sync.Mutex 和 sync.RWMutex。

对于任意 sync.Mutex 或 sync.RWMutex 变量l。 如果 n < m ，那么第n次 l.Unlock() 调用在第 m次 l.Lock()调用返回前发生。

例如程序：
```
var l sync.Mutex
var a string

func f() {
        a = "hello, world";
        l.Unlock();
}

func main() {
        l.Lock();
        go f();
        l.Lock();
        print(a);
}
```

可以确保输出“hello, world”结果。因为，第一次 l.Unlock() 调用（在f函数中）在第二次 l.Lock() 调用（在main 函数中）返回之前发生，也就是在 print 函数调用之前发生。

## Once

包once提供了一个在多个goroutines中进行初始化的方法。多个goroutines可以 通过 once.Do(f) 方式调用f函数。但是，f函数 只会被执行一次，其他的调用将被阻塞直到唯一执行的f()返回。once.Do(f) 中唯一执行的f()发生在所有的 once.Do(f) 返回之前。

```
var a string

func setup() {
        a = "hello, world";
}

func doprint() {
        once.Do(setup);
        print(a);
}

func twoprint() {
        go doprint();
        go doprint();
}
```
调用twoprint会输出“hello, world”两次。第一次twoprint 函数会运行setup唯一一次。


---

## Goroutine 原理

### 为什么Goroutine这么快？

首先协程是一种用户态的轻量级线程，协程的调度完全由用户控制，协程间切换只需要保存任务的上下文，没有内核的开销。

让我们分析一下什么让线程这么慢：

- 上下文切换的代价是高昂的，因为在核心上交换线程会花费很多时间。上下文切换的延迟取决于不同的因素，大概在在 50 到 100 纳秒之间。考虑到硬件平均在每个核心上每纳秒执行 12 条指令，那么一次上下文切换可能会花费 600 到 1200 条指令的延迟时间。实际上，上下文切换占用了大量程序执行指令的时间。

- 如果存在跨核上下文切换（Cross-Core Context Switch），可能会导致 CPU 缓存失效（CPU 从缓存访问数据的成本大约 3 到 40 个时钟周期，从主存访问数据的成本大约 100 到 300 个时钟周期），这种场景的切换成本会更加昂贵。

所以说 Goroutine分别作了优化：

Goroutine 非常轻量，主要体现在以下两个方面：

- 上下文切换代价小： Goroutine 上下文切换只涉及到三个寄存器（PC / SP / DX）的值修改；而对比线程的上下文切换则需要涉及模式切换（从用户态切换到内核态）、以及 16 个寄存器、PC、SP…等寄存器的刷新；

- 内存占用少：线程栈空间通常是 2M，Goroutine 栈空间最小 2K；Golang 程序中可以轻松支持10w 级别的 Goroutine 运行，而线程数量达到 1k 时，内存占用就已经达到 2G。

### Go的G-M-P模型：

#### G

Go 调度器模型我们通常叫做G-P-M 模型，他包括 4 个重要结构，分别是G、P、M、Sched：

G:Goroutine，每个 Goroutine 对应一个 G 结构体，G 存储 Goroutine 的运行堆栈、状态以及任务函数，可重用。

G 并非执行体，每个 G 需要绑定到 P 才能被调度执行。

#### P

P: Processor，表示逻辑处理器，对 G 来说，P 相当于 CPU 核，G 只有绑定到 P 才能被调度。对 M 来说，P 提供了相关的执行环境(Context)，如内存分配状态(mcache)，任务队列(G)等。

P 的数量决定了系统内最大可并行的 G 的数量（前提：物理 CPU 核数 >= P 的数量）。

P 的数量由用户设置的 GoMAXPROCS 决定，但是不论 GoMAXPROCS 设置为多大，P 的数量最大为 256。

#### M

M: Machine，OS 内核线程抽象，代表着真正执行计算的资源，在绑定有效的 P 后，进入 schedule 循环；而 schedule 循环的机制大致是从 Global 队列、P 的 Local 队列以及 wait 队列中获取。

M 的数量是不定的，由 Go Runtime 调整，为了防止创建过多 OS 线程导致系统调度不过来，目前默认最大限制为 10000 个。

M 并不保留 G 状态，这是 G 可以跨 M 调度的基础。

#### Sched

Sched：Go 调度器，它维护有存储 M 和 G 的队列以及调度器的一些状态信息等。

![](https://pic3.zhimg.com/80/v2-a27259141ff915578ab5165d75432930_720w.jpg)

地鼠(Gopher)的工作任务是：工地上有若干砖头，地鼠借助小车把砖头运送到火种上去烧制。M 就可以看作图中的地鼠，P 就是小车，G 就是小车里装的砖。

Go 调度器中有两个不同的运行队列：全局运行队列(GRQ)和本地运行队列(LRQ)。

每个 P 都有一个 LRQ，用于管理分配给在 P 的上下文中执行的 Goroutines，这些 Goroutine 轮流被和 P 绑定的 M 进行上下文切换。GRQ 适用于尚未分配给 P 的 Goroutines。

从上图可以看出，G 的数量可以远远大于 M 的数量，换句话说，Go 程序可以利用少量的内核级线程来支撑大量 Goroutine 的并发。多个 Goroutine 通过用户级别的上下文切换来共享内核线程 M 的计算资源，但对于操作系统来说并没有线程上下文切换产生的性能损耗。

### 调度策略：

为了更加充分利用线程的计算资源，Go 调度器采取了以下几种调度策略：

#### 任务窃取（work-stealing）

我们知道，现实情况有的 Goroutine 运行的快，有的慢，那么势必肯定会带来的问题就是，忙的忙死，闲的闲死，Go 肯定不允许摸鱼的 P 存在，势必要充分利用好计算资源。

为了提高 Go 并行处理能力，调高整体处理效率，当每个 P 之间的 G 任务不均衡时，调度器允许从 GRQ，或者其他 P 的 LRQ 中获取 G 执行。

#### 减少阻塞

如果正在执行的 Goroutine 阻塞了线程 M 怎么办？P 上 LRQ 中的 Goroutine 会获取不到调度么？

1. 由于原子、互斥量或通道操作调用导致 Goroutine 阻塞，调度器将把当前阻塞的 Goroutine 切换出去，重新调度 LRQ 上的其他 Goroutine；

2. 由于网络请求和 IO 操作导致 Goroutine 阻塞 --> 网络轮询器（NetPoller）来处理网络请求和 IO 操作的问题, 在需求网络的时候，会被移动到网络轮询器并且处理异步网络系统调用。然后，M 可以从
LRQ 执行另外的 Goroutine。最后，异步网络系统调用由网络轮询器完成，G1 被移回到 P 的 LRQ 中。一旦 G1 可以在 M 上进行上下文切换，它负责的 Go 相关代码就可以再次执行。

3. 如果系统方法调用的时候发生阻塞，调度器介入后：识别出 G1 已导致 M1 阻塞，此时，调度器将 M1 与 P 分离，同时也将 G1 带走。然后调度器引入新的 M2 来服务 P。此时，可以从 LRQ 中选择 G2 并在 M2 上进行上下文切换。

4. 如果在 Goroutine 去执行一个 sleep 操作，导致 M 被阻塞了。Go 程序后台有一个监控线程 sysmon，它监控那些长时间运行的 G 任务然后设置可以强占的标识符，别的 Goroutine 就可以抢先进来执行。只要下次这个 Goroutine 进行函数调用，那么就会被强占，同时也会保护现场，然后重新放入 P 的本地队列里面等待下次执行。

