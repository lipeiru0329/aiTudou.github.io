---
title: go-java
date: 2020-08-11 17:17:49
tags:
---

## Go VS Java

<!-- More -->

来列一下Go的不足吧：

1. 没有泛型
2. 标准库中数据结构远不如Java丰富
3. 依赖管理鸡肋
4. channel如果使用不当，非常容易死锁
5. 编译时不允许循环import
6. 编码时很多包名会跟自定义的变量名冲突
7. Java中很多认为理所当然的库，在Go中发现要重新造轮子
8. go get命令会clone整个git仓库，包括历史commit
9. 标准库不提供routine池
10. 没有现成的Future机制 ，要自己通过channel实现
11. 起新routine时一定要注意recover兜底，否则万一panic了，整个程序都挂了。

Java 的问题：

1. 语言表达能力比较欠缺(接地气的说法叫“又臭又长”)
2. 内存、CPU消耗大
3. 堆内存较大时，垃圾回收器需要进行深入调优才能得到满意的回收效果; 然而在一些对实时性要求高的场景下，gc可能直接就是无解, full gc一触发就是地狱
4. 程序需要预热
5. JDK体积庞大, springboot jar包体积大(在微服务架构下问题最突出)
6. Spring全家桶越来越重(Spring你做好IoC AOP就够了)，导致使用全家桶的应用，性能较差(可参考TechEmpower Round 14中spring的位置)，但也是足够用的
7. 因为成熟的框架、库太多，导致很多人入门时被带偏，以为编程就是调API，用框架，而对于原理知之甚少

---

## JVM 预热

JVM预热 ： JVM Warm Up

一旦类加载完成，所有重要的类（在进程启动时使用）都会被推送到JVM缓存（本机代码）中，这使得它们在运行时可以更快地访问。其他类是根据每个请求加载的。
对Java Web应用程序的第一个请求通常比进程的生命周期中的平均响应时间慢得多。这个预热期通常可以归因于延迟类加载和及时编译。
记住，对于低延迟应用程序，我们需要预先缓存所有类，以便在运行时访问时立即可用。
这种调优JVM的过程称为预热。

```
public class Dummy {
	public void m() {
		
	}
}
```
```
public class ManualClassLoader {
	protected static void load() {
		for (int i = 0; i < 100000; i++) {
			Dummy dummy = new Dummy();
			dummy.m();
		}
	}
}
```
```
public class MainApplication {
	
	static {
		long start = System.nanoTime();
		ManualClassLoader.load();
		long end = System.nanoTime();
		System.out.println("Warm Up time : " + (end - start));
	}
	
	public static void main(String[] args) {
		long start = System.nanoTime();
		ManualClassLoader.load();
		long end = System.nanoTime();
		System.out.println("Total time taken : " + (end - start));
	}
}
```
```
Warm Up time : 5213663
Total time taken : 872901
```

预热： [Java Microbenchmark Harness](https://search.maven.org/classic/#search%7Cga%7C1%7Corg.openjdk.jmh)

---

## Go 效率

Golang 默认一种垃圾回收策略，走的是高频次低延迟的路线，而默认Java/C#是高吞吐量高延迟的，所以这点非常不利于Go的性能表现。如果是游戏的话，用java实现会出现周期性的卡顿现象，如果是 Go 的话就不会有这种现象，这是高频低延的好处。另外这个案例也切中了Java/C#的分代垃圾回收算法，大量的小内存在复制回收算法中是非常快的。不过Go可以做的更快 —— 实现定义的内存池，这样的话它的速度应该可以接近C/C++的实现，事实上有人提交过这样的代码被拒了，这种思路在 Java 上是无法实现的因为Java的内存分布是JVM控制的无法自定义内存，用C#的struct应该也可以达到类似的效果。从语言对内存管理的天然亲昵性来看：C/C++ > Go > C# > Java 的，这个特性其实决定了已经优化过执行策略的语言的性能上限，这是因为现在的CPU瓶颈往往在于内存加载使用连续分布的内存才能实现更快的应用。

---

## 内存池 tcmalloc

tcmalloc就是一个内存分配器，管理堆内存，主要影响malloc和free，用于降低频繁分配、释放内存造成的性能损耗，并且有效地控制内存碎片。glibc中的内存分配器是ptmalloc2，tcmalloc号称要比它快。一次malloc和free操作，ptmalloc需要300ns，而tcmalloc只要50ns。同时tcmalloc也优化了小对象的存储，需要更少的空间。tcmalloc特别对多线程做了优化，对于小对象的分配基本上是不存在锁竞争，而大对象使用了细粒度、高效的自旋锁（spinlock）。分配给线程的本地缓存，在长时间空闲的情况下会被回收，供其他线程使用，这样提高了在多线程情况下的内存利用率，不会浪费内存，而这一点ptmalloc2是做不到的。

tcmalloc区别的对待大、小对象。它为每个线程分配了一个线程局部的cache，线程需要的小对象都是在其cache中分配的，由于是thread local的，所以基本上是无锁操作（在cache不够，需要增加内存时，会加锁）。同时，tcmalloc维护了进程级别的cache，所有的大对象都在这个cache中分配，由于多个线程的大对象的分配都从这个cache进行，所以必须加锁访问。在实际的程序中，小对象分配的频率要远远高于大对象，通过这种方式（小对象无锁分配，大对象加锁分配）可以提升整体性能。