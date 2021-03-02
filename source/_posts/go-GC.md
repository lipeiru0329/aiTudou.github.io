---
title: go-GC
date: 2020-08-03 14:33:36
tags:
---


## Go 的GC 机制(~1.3)

<!-- More -->

golang的垃圾回收采用的是 标记-清理（Mark-and-Sweep） 算法

### 触发GC

触发GC机制
1. 在申请内存的时候，检查当前当前已分配的内存是否大于上次GC后的内存的2倍，若是则触发（主GC线程为当前M）

2. 监控线程发现上次GC的时间已经超过两分钟了，触发；将一个G任务放到全局G队列中去。（主GC线程为执行这个G任务的M）

### 每当触发的时候，在主GC线程中就会走如下的GC流程：

1. stop the world，等待所有的M休眠；此时所有的业务逻辑代码都停止

2. 标记：分配gc标记任务，唤醒 gcproc个 M（就是第一步休眠的那些），分别做这个，直到所有的M都做完，才结束；并且所有M再次进入休眠

3. 清理：有一个单独的goroutine去清理已经标记的内存对象快

4. start the world，设置gcwaiting=0，唤醒所有的M（不会超过P个数）

### 对于上面的三个步骤，分别解释：

Stop the world：
    设置gcwaiting=1，这个在每一个G任务之前会检查一次这个状态，如是，则会将当前M 休眠；

    如果这个M里面正在运行一个长时间的G任务，咋办呢，难道会等待这个G任务自己切换吗？这样的话可要等10ms啊，不能等！坚决不能等！ 所以会主动发出抢占标记（类似于上一篇），让当前G任务中断，再运行下一个G任务的时候，就会走到第1步

    一直等待所有的M进入休眠，此时所有的业务逻辑代码都停止

标记：
    根据gcproc的个数，分配成gcproc任务段；唤醒gcproc-1 个M来执行（当前M也算一个）

    对于一个M，唤醒前设置它的helpgc标记，唤醒之后这个M会立马判断这个标记，如是，则开始做分配给自己的标记任务，如果先做完了，就会从别的M里面找一些来做

    等每一个M都做完，会再次进入休眠

清理：
    通过设置参数，可以以一个单独goroutine 运行，这个功能是在1.3版本之后增加的，这样的话就直接到下一步了，清理过程不是stw的

    也可以串行的在主GC线程执行；这样的话则清理过程也是stw的，

Start the world：
    设置gcwaiting=0

    唤醒P个M来继续做G任务（此时没有helpgc标记），业务逻辑代码开始

---

## Go的三色清理（1.15版本之后的GC)


1. 起初所有对象都是白色。
1. 从根出发扫描所有可达对象，标记为灰色，放入待处理队列。
1. 从队列取出灰色对象，将其引用对象标记为灰色放入队列，自身标记为黑色。
1. 重复 3，直到灰色对象队列为空。此时白色对象即为垃圾，进行回收。

![](https://segmentfault.com/img/remote/1460000018161591)

go 1.5正在实现的垃圾回收器是“非分代的、非移动的、并发的、三色的标记清除垃圾收集器”。引入了上文介绍的三色标记法，这种方法的mark操作是可以渐进执行的而不需每次都扫描整个内存空间，可以减少stop the world的时间。 由此可以看到，一路走来直到1.5版本，go的垃圾回收性能也是一直在提升，但是相对成熟的垃圾回收系统（如java jvm和javascript v8），go需要优化的路径还很长（但是相信未来一定是美好的~）。

## Go 1.8 GC

这个版本的 GC 代码相比之前改动还是挺大的，采用一种混合的 write barrier 方式 （Yuasa-style deletion write barrier [Yuasa ‘90] 和 Dijkstra-style insertion write barrier [Dijkstra ‘78]）来避免 堆栈重新扫描。

混合屏障的优势在于它允许堆栈扫描永久地使堆栈变黑（没有STW并且没有写入堆栈的障碍），这完全消除了堆栈重新扫描的需要，从而消除了对堆栈屏障的需求。重新扫描列表。特别是堆栈障碍在整个运行时引入了显着的复杂性，并且干扰了来自外部工具（如GDB和基于内核的分析器）的堆栈遍历。

此外，与Dijkstra风格的写屏障一样，混合屏障不需要读屏障，因此指针读取是常规的内存读取; 它确保了进步，因为物体单调地从白色到灰色到黑色。

## 总结

通过go team多年对gc的不断改进和忧化，GC的卡顿问题在1.8 版本基本上可以做到 1 毫秒以下的 GC 级别。 实际上，gc低延迟是有代价的，其中最大的是吞吐量的下降。由于需要实现并行处理，线程间同步和多余的数据生成复制都会占用实际逻辑业务代码运行的时间。GHC的全局停止GC对于实现高吞吐量来说是十分合适的，而Go则更擅长与低延迟。  

并行GC的第二个代价是不可预测的堆空间扩大。程序在GC的运行期间仍能不断分配任意大小的堆空间，因此我们需要在到达最大的堆空间之前实行一次GC，但是过早实行GC会造成不必要的GC扫描，这也是需要衡量利弊的。因此在使用Go时，需要自行保证程序有足够的内存空间。

---

## Golang 1.13 - 1.14 GC

Scavenger 已经从独立线程中移除，并合并至系统监控这个独立的线程中，并周期性地向操作系统归还内存

- 1.13解决的问题：仍然会有内存溢出这种比较极端的情况出现，因为程序可能在短时间内应对突发性的内存申请需求时，内存还没来得及归还操作系统，导致堆不断向操作系统申请内存，从而出现内存溢出。

    --> Go 团队开始将周期性的 Scavenger 转化为可被调度的 goroutine

- Go 1.14，这一向操作系统归还内存的操作时间进一步得到缩减。
