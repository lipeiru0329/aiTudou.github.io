---
title: golang-注意
date: 2020-06-29 23:23:30
tags:
---

## Golang 注意

### slice之append时原数组发生变化

<!-- More -->

```
nums:=[]int{1,2,3}
result:=append(nums,4)
fmt.Println(result)
```

这段代码很简单，输出result的值为：[1 2 3 4]
问题在于，进行这种操作时，原来的slice（即nums）所基于的数组的值 是否 发生变化

> 在Golang中，如果有多个slice基于了同一个数组，则这些slice的数据是共享的（而不是每个slice复制一份）,也就说，如果改变了数组的内容，则基于它的所有slice的值都会变化这段代码中nums的值没有变化，但是并非所有时候都是如此。

回答这个问题，首先需要了解append函数实现原理：

1. 如果nums的cap够用，则会直接在nums指向的数组后面追加元素，返回的slice和原来的slice是同一个对象。显然，这种情况下原来的slice的值会发生变化！
2. 如果nums的cap不够用（上述代码就是这种情况），则会重新分配一个数组空间用来存储数据，并且返回指向新数组的slice。这时候原来的slice指向的数组并没有发生任何变化！
3. 当然，在任何情况下，返回的结果都是追加之后的slice，这一点没有问题！
```
func test1() {
    nums := []int{1, 2, 3}
    _ = append(nums[:2], 4)
    fmt.Println("test1:", nums)

    //nums changes because the cap is big enought, the original array is modified.

}

func test2() {
    nums := []int{1, 2, 3}
    c := append(nums[:2], []int{4, 5, 6}...)
    fmt.Println("test2:", nums)
    fmt.Println("cc:", c)

    //nums dont't change because the cap isn't big enought.
    //a new array is allocated while the nums still points to the old array.
    //Of course, the return value of append points to the new array.
}
```

---

Golang 深拷贝浅拷贝

1. 值类运算深拷贝
2. slice map 浅拷贝
3. slice append 之后可能会新建slice 然后切断之前的联系