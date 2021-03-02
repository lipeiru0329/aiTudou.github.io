---
title: SpringRedis
date: 2020-05-27 17:00:49
tags:
disqusId: aitudou-blog
---

## Redis官方对Java语言的封装框架推荐有十多种，主要有：Jedis、Lettuce、Redisson

## Redisson:

https://juejin.im/post/5da287b55188256f4e70752b

https://www.ibm.com/developerworks/cn/java/know-redis-and-use-it-in-springboot-projects/index.html

安装 Redisson 最便捷的方法是使用 Maven 或者 Gradle：

<!-- More -->

```
Maven

<dependency>
    <groupId>org.redisson</groupId>
    <artifactId>redisson</artifactId>
    <version>3.13.0</version>
</dependency>
```

```
Gradle

compile group: 'org.redisson', name: 'redisson', version: '3.13.0'

```


### 如何编译运行 Redisson

安装 Redisson 后，只需使用 Java 编译器即可编译和运行 Redisson 代码：

```
javac RedissonExamples.java

java RedissonExamples
```

## Jedis

Jedis是Redis的Java实现的客户端，其API提供了比较全面的Redis命令的支持。支持基本的数据类型如：String、Hash、List、Set、Sorted Set。

优点：比较全面的提供了Redis的操作特性，相比于其他Redis 封装框架更加原生。

编程模型： 使用阻塞的I/O，方法调用同步，程序流需要等到socket处理完I/O才能执行，不支持异步操作。Jedis客户端实例不是线程安全的，所以需要通过连接池来使用Jedis。




问题：
1. Redis client 占内存
2. 内存清楚

--> 压力测试