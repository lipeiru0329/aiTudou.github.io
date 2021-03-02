---
title: nodejs
date: 2020-12-23 14:53:46
tags:
---

## Nodejs

### 这节课讲Nodejs

1. 
- 栈内存一般储存基础数据类型(Stack: 后进先出)
- 堆内存一般储存引用数据类型 (map, array 类比Go的map和slice)

2. 维护task queue，包括await，timeout 类比Go的GPM模型

3. 栈：

- 存储基础数据类型
- 按值访问
- 存储的值大小固定
- 由系统自动分配内存空间
- 空间小，运行效率高
- 先进后出，后进先出
栈中的DOM，ajax，setTimeout会依次进入到队列中,当栈中代码执行完毕后，再将队列中的事件放到执行栈中- 依次执行。
- 微任务和宏任务



堆:

- 存储引用数据类型
- 按引用访问
- 存储的值大小不定，可动态调整
- 主要用来存放对象
- 空间大，但是运行效率相对较低
- 无序存储，可根据引用直接获取

4. 
- Promise.all will reject as soon as one of the Promises in the array rejects.
- Promise.allSettled will never reject - it will resolve once all Promises in the array have either rejected or resolved.