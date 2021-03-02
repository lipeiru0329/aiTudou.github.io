---
title: go-channel
date: 2020-07-05 16:25:20
tags:
---

## Golang 的 channel 和 线程安全

本节讲缓冲的channel

<!-- More -->

基本知识：阻塞：在执行过程中暂停，以等待某个条件的触发

- 在Go中我们make一个channel有两种方式，分别是有缓冲的和没缓冲的
    - 缓冲channel 即 buffer channel 创建方式为 make(chan TYPE,SIZE)
        
        如 make(chan int,3) 就是创建一个int类型，缓冲大小为3的 channel
    - 非缓冲channel 即 unbuffer channel 创建方式为 make(chan TYPE)
        
        如 make(chan int) 就是创建一个int类型的非缓冲channel

- 非缓冲channel 和 缓冲channel 的区别
    - 非缓冲 channel，channel 发送和接收动作是同时发生的
        
        例如 ch := make(chan int) ，如果没 goroutine 读取接收者<-ch ，那么发送者ch<- 就会一直阻塞
    - 缓冲 channel 类似一个队列，只有队列满了才可能发送阻塞

非缓冲：
```
package main

import (
	"fmt"
	"time"
)

func loop(ch chan int) {
	for {
		select {
		case i := <-ch:
			fmt.Println("this  value of unbuffer channel", i)
		}
	}
}

func main() {
	ch := make(chan int)
	go loop(ch)  
	ch <- 1         // 如果channel 放在go loop 上面的话 就会报错 因为没有人能接受
	time.Sleep(1 * time.Millisecond)
```

缓冲：
```
package main

import (
	"fmt"
	"time"
)

func loop(ch chan int) {
	for {
		select {
		case i := <-ch:
			fmt.Println("this  value of unbuffer channel", i)
		}
	}
}

func main() {
	ch := make(chan int,3)
	ch <- 1
	ch <- 2
	ch <- 3
    go loop(ch)
	ch <- 4         // 因为buffer（FIFO) 只有3个容积，必须在这个之前开始消费
	time.Sleep(1 * time.Millisecond)
}
```

### 线程安全

既然有channel 那golang又是怎么做线程安全呢

首先 golang 是用 goroutine 来 做多线程的 

#### Goroutine vs thread vs process

__Goroutine__: A Goroutine is a function or method which executes independently and simultaneously in connection with any other Goroutines present in your program. Or in other words, every concurrently executing activity in Go language is known as a Goroutines.

__Thread__: A process is a part of an operating system which is responsible for executing an application. Every program that executes on your system is a process and to run the code inside the application a process uses a term known as a thread. A thread is a lightweight process, or in other words, a thread is a unit which executes the code under the program. So every program has logic and a thread is responsible for executing this logic.

|GOROUTINE |	THREAD|
|----| --- |
|Goroutines are managed by the go runtime.|	Operating system threads are managed by kernal.|
|Goroutine are not hardware dependent.	|Threads are hardware dependent.|
|Goroutines have easy communication medium known as channel.|	Thread doesnot have easy communication medium.|
|Due to the presence of channel one goroutine can communicate with other goroutine with low latency.	|Due to lack of easy communication medium inter-threads communicate takes place with high latency.|
|Goroutine doesnot have ID because go doesnot have Thread Local Storage.|	Threads have their own unique ID because they have Thread Local Storage.|
|Goroutines are cheaper than threads.|	The cost of threads are higher than goroutine.|
|They are cooperatively scheduled.|	They are preemptively scheduled.|
|They have fasted startup time than threads.	|They have slow startup time than goroutines.|
|Goroutine has growable segmented stacks.|	Threads doesnot have growable segmented stacks.|

1. goroutine执行完后立刻执行下一个goroutine

```
var wg sync.WaitGroup
for i := 0; i < 10; i++ {
    wg.Add(1)//每启动一个协程增加一个等待
    go func() {
        fmt.Println(i)
        wg.Done()//告诉协成等待的事务已经完成
    }()
}
```
2. 每个线程拿到的数是惟一的

这就需要你传入数据
```
var wg sync.WaitGroup
for i := 0; i < 10; i++ {
    wg.Add(1)//每启动一个协程增加一个等待
    go func(j int) {//把j只是个形参可以任意命名
        fmt.Println(j)
        wg.Done()//告诉协成等待的事务已经完成
    }(i)//把实参i传递给形参j
```

3. 当然加锁肯定可以的

```
func TestCounterWaitGroup(t *testing.T) {
    var mut sync.Mutex//创建锁对象
    var wg sync.WaitGroup
    counter := 0
    for i := 0; i < 5000; i++ {
        wg.Add(1)//每启动一个协程增加一个等待
        go func() {
            defer func() {
                mut.Unlock()//释放锁
            }()
            mut.Lock()//开启锁
            counter++
            wg.Done()//告诉协成等待的事务已经完成
        }()
    }
    wg.Wait()//等待协程
    t.Logf("counter = %d", counter)
}
```
