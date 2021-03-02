---
title: golang-goroutine-进阶
date: 2020-08-07 13:39:11
tags:
---

## Golang的Goroutine进阶

这次来说 Goroutine的控制和并发

### 全局的Goroutine的控制

- 全局共享变量

```
    control := true
	abc := func() {
		for {
			if control == false {
				return
			}
			fmt.Println("Routine")
			time.Sleep(1 * time.Second)
		}
	}

	go abc()
	go abc()
	go abc()

	time.Sleep(3 * time.Second)

	control = false
```

- Channel

Channel是Go中的⼀个核⼼类型，你可以把它看成⼀个管道，通过它并发核⼼单元就可以发送或者接收数据进⾏通讯(communication)。

CSP 是 Communicating Sequential Process 的简称，中⽂可以叫做通信顺序进程，是⼀种并发编程模型，由 Tony Hoare 于1977 年提出。简单来说，CSP 模型由并发执⾏的实体（线程或者进程）所组成，实体之间通过发送消息进⾏通信，这⾥发送消息时使⽤的就是通道，或者叫 channel。CSP 模型的关键是关注 channel，⽽不关注发送消息的实体。Go 语⾔实现了 CSP 部分理论，goroutine 对应 CSP 中并发执⾏的实体，channel 也就对应着 CSP 中的 channel。

也就是说，CSP 描述这样⼀种并发模型：多个Process 使⽤⼀个 Channel 进⾏通信, 这个 Channel 连结的 Process 通常是匿名的，消息传递通常是同步的（有别于 Actor Model）。

```
func Routine(check <- chan int) {
	for {
		select {
		case <-check:
			fmt.Println("Stop")
			return
		default:
			fmt.Println("Rountine")
			time.Sleep(1 * time.Second)
		}
	}
}

func main() {

	check := make(chan int, 1)
	go Routine(check)
	time.Sleep(3 * time.Second)
	check <- 1
}
```

但是 这样的话 主进程结束的时候 所有的goroutine也将结束，如果不想的话需要用sync包

```
var wd sync.WaitGroup

func Routine(check <- chan int) {
	for {
		select {
		case <-check:
			fmt.Println("Stop")
			time.Sleep(1 * time.Second)
			fmt.Println("Routine stop")
			return
		default:
			fmt.Println("Rountine")
			time.Sleep(1 * time.Second)
		}
	}
}

func main() {

	check := make(chan int, 1)


	for i := 0; i < 3; i ++ {
		wd.Add(1)
		go func(check chan int) {
			Routine(check)
			wd.Done()
			//defer wd.Done()
		}(check)
	}

	//go Routine(check)
	time.Sleep(3 * time.Second)
	check <- 1           // 这个地方需要消费3次，因为之前有3个consumer
	check <- 1          
	check <- 1
	//close(check)          // 或者直接close channel
	fmt.Println("Main stop")
	wd.Wait()
}
```
