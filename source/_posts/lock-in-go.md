---
title: lock-in-go
date: 2020-08-12 14:27:47
tags:
---

## Lock in go

这期讲讲Go里面的锁

### 死锁


死锁是指两个或两个以上的进程在执行过程中，由于竞争资源或者由于彼此通信而造成的一种阻塞的现象，若无外力作用，它们都将无法推进下去。此时称系统处于死锁状态或系统产生了死锁，

示例代码：

```
package main

import "fmt"

func main() {
    ch := make(chan int)
    ch <- 1         // I'm blocked because there is no channel read yet. 
    fmt.Println("send")
    go func() {
        <-ch        // I will never be called for the main routine is blocked!
        fmt.Println("received")
    }()
    fmt.Println("over")
}
```

解决方法： channel加buffer

```
func main() {

	check := make(chan int, 1)

	check <- 1
	fmt.Println("send")
	go func() {
		<-check       
		fmt.Println("received")
	}()
	fmt.Println("over")
	time.Sleep(1*time.Second)
}
```

### 互斥锁

每个资源都对应于一个可称为 "互斥锁" 的标记，这个标记用来保证在任意时刻，只能有一个 go 程（线程）访问该资源。其它的 go 程只能等待。

互斥锁是传统并发编程对共享资源进行访问控制的主要手段，它由标准库 sync 中的 Mutex 结构体类型表示。sync.Mutex 类型只有两个公开的指针方法，Lock 和 Unlock。Lock 锁定当前的共享资源，Unlock 进行解锁。

在使用互斥锁时，一定要注意：对资源操作完成后，一定要解锁，否则会出现流程执行异常，死锁等问题。通常借助 defer。锁定后，立即使用 defer 语句保证互斥锁及时解锁。如下所示：

```
var mutex sync.Mutex        // 定义互斥锁变量 mutex

func write(){
   mutex.Lock( )
   defer mutex.Unlock( )
}
```

### 读写锁

读写锁可以让多个读操作并发，同时读取，但是对于写操作是完全互斥的。也就是说，当一个 goroutine 进行写操作的时候，其他 goroutine 既不能进行读操作，也不能进行写操作。

GO 中的读写锁由结构体类型 sync.RWMutex 表示。此类型的方法集合中包含两对方法：

一组是对写操作的锁定和解锁，简称 “写锁定” 和 “写解锁”：

```
func (*RWMutex)Lock()
func (*RWMutex)Unlock()
```

另一组表示对读操作的锁定和解锁，简称为 “读锁定” 与 “读解锁”：
```
func (*RWMutex)RLock()
func (*RWMutex)RUnlock()
```