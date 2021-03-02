---
title: spring-1
date: 2020-10-27 17:55:47
tags:
---

## Spring 基础

### Spring基础知识：

#### 注入

1. @Autowired
 开启： @SpringBootApplication = @Configuration, @EnableAutoConfiguration, and @ComponentScan.

 - 类
 - 类字段
 2. set方法
 3. 构造器
 4. factory
- 就是说 多个子类继承父类，initial的时候 可以返回不同的子类

- 工厂： 抽象 信息不同 方法相同
        静态

#### new方法

- 构造器

- factory 注入

#### 注入 vs new

spring实现了对象池，一些对象创建和使用完毕之后不会被销毁，放进对象池（某种集合）以备下次使用，下次再需要这个对象，不new，直接从池里出去来用。节省时间，节省cpu.

#### Spring的事务


        事务特性（4种）:
        
        - 原子性 （atomicity）:强调事务的不可分割，要么一起成功,要么一起失败。
        - 一致性 （consistency）:事务的执行时，前后数据的完整性保持一致。
        - 隔离性 （isolation）:一个事务执行的过程中,不应该受到其他事务的干扰。
        - 持久性（durability） :事务一旦结束,数据就持久到数据库。

        问题：

        - Dirty read
        - unrepeatable read
        - phantom read

        Non-repeatable reads are when your transaction reads committed UPDATES from another transaction. The same row now has different values than it did when your transaction began.

        Phantom reads are similar but when reading from committed INSERTS and/or DELETES from another transaction. There are new rows or rows that have disappeared since you began the transaction.

        Dirty reads are similar to non-repeatable and phantom reads, but relate to reading UNCOMMITTED data, and occur when an UPDATE, INSERT, or DELETE from another transaction is read, and the other transaction has NOT yet committed the data. It is reading "in progress" data, which may not be complete, and may never actually be committed.

        事务隔离级别（5种）

        解决读问题：
        - DEFAULT ：这是一个PlatfromTransactionManager默认的隔离级别，使用数据库默认的事务隔离级别。
        - 读未提交（read uncommited）：脏读，不可重复读，虚读都有可能发生。
        - 读已提交 （read commited）：避免脏读。但是不可重复读和虚读有可能发生。
        - 可重复读 （repeatable read）：避免脏读和不可重复读.但是虚读有可能发生。
        - 串行化的 （serializable）：避免以上所有读问题。

---

#### 父子类事务的继承，子类会继承父类的事务级别

---

#### IOC APO

IOC: 控制反转也叫依赖注入。利用了工厂模式

> 将对象交给容器管理，你只需要在spring配置文件总配置相应的bean，以及设置相关的属性，让spring容器来生成类的实例对象以及管理对象。在spring容器启动的时候，spring会把你在配置文件中配置的bean都初始化好，然后在你需要调用的时候，就把它已经初始化好的那些bean分配给你需要调用这些bean的类（假设这个类名是A），分配的方法就是调用A的setter方法来注入，而不需要你在A里面new这些bean了。注意：面试的时候，如果有条件，画图，这样更加显得你懂了.

AOP:面向切面编程。（Aspect-Oriented Programming）

>AOP可以说是对OOP的补充和完善。OOP引入封装、继承和多态性等概念来建立一种对象层次结构，用以模拟公共行为的一个集合。当我们需要为分散的对象引入公共行为的时候，OOP则显得无能为力。也就是说，OOP允许你定义从上到下的关系，但并不适合定义从左到右的关系。例如日志功能。日志代码往往水平地散布在所有对象层次中，而与它所散布到的对象的核心功能毫无关系。在OOP设计中，它导致了大量代码的重复，而不利于各个模块的重用。

>将程序中的交叉业务逻辑（比如安全，日志，事务等），封装成一个切面，然后注入到目标对象（具体业务逻辑）中去。

>实现AOP的技术，主要分为两大类：一是采用动态代理技术，利用截取消息的方式，对该消息进行装饰，以取代原有对象行为的执行；二是采用静态织入的方式，引入特定的语法创建“方面”，从而使得编译器可以在编译期间织入有关“方面”的代码.

>简单点解释，比方说你想在你的biz层所有类中都加上一个打印‘你好’的功能,这时就可以用aop思想来做.你先写个类写个类方法，方法经实现打印‘你好’,然后Ioc这个类 ref＝“biz.*”让每个类都注入即可实现。