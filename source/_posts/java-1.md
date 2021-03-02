---
title: java-1
date: 2020-10-26 14:26:21
tags:
---

## Java 

从本节开始因为开始准备Java面试，所以说想写一下总结

<!-- More -->

### Java 基本知识：

#### JRE VS JDK

- JDK：Java Development Kit 的简称，java 开发工具包，提供了 java 的开发环境和运行环境。
- JRE：Java Runtime Environment 的简称，java 运行环境，为 java 的运行提供了所需环境。

具体来说 JDK 其实包含了 JRE，同时还包含了编译 java 源码的编译器 javac，还包含了很多 java 程序调试和分析的工具。简单来说：如果你需要运行 java 程序，只需安装 JRE 就可以了，如果你需要编写 java 程序，需要安装 JDK。

#### == vs equals

- == ： reference 比较
- equals： 值比较

### hashCode() 

- 哈希碰撞：

    1. 开放地址法(再散列法) -- hash之后 再去运算公式
    2. 再哈希法Rehash -- hash之后 再去hash
    3. 链地址法（拉链法）hash 相同建一个list

        优点：
            拉链法处理冲突简单，且无堆积现象，即非同义词决不会发生冲突，因此平均查找长度较短；
            
            由于拉链法中各链表上的结点空间是动态申请的，故它更适合于造表前无法确定表长的情况；
            
            开放定址法为减少冲突，要求装填因子α较小，故当结点规模较大时会浪费很多空间。而拉链法中可取α≥1，且结点较大时，拉链法中增加的指针域可忽略不计，因此节省空间；
            
            在用拉链法构造的散列表中，删除结点的操作易于实现。只要简单地删去链表上相应的结点即可。而对开放地址法构造的散列表，删除结点不能简单地将被删结 点的空间置为空，否则将截断在它之后填人散列表的同义词结点的查找路径。这是因为各种开放地址法中，空地址单元(即开放地址)都是查找失败的条件。因此在 用开放地址法处理冲突的散列表上执行删除操作，只能在被删结点上做删除标记，而不能真正删除结点。

        缺点：

            指针需要额外的空间，故当结点规模较小时，开放定址法较为节省空间，而若将节省的指针空间用来扩大散列表的规模，可使装填因子变小，这又减少了开放定址法中的冲突，从而提高平均查找速度。

### Final：

- initial 修饰的类叫最终类，该类不能被继承。
- final 修饰的方法不能被重写。
- final 修饰的变量叫常量，常量必须初始化，初始化之后值就不能被修改。

#### 基本数据类型

基础类型有 8 种：byte、boolean、char、short、int、float、long、double，而 String, Integer, BigDecimal... 属于对象。

#### String, StringBuffer, StringBuilder

1. String 声明的是不可变的对象，每次操作都会生成新的 String 对象
2. StringBuffer 是线程安全的，
3. StringBuilder 是非线程安全的

---

#### 小知识

stringBuilder/stringBuffer

1. reverse()
2. indexOf()：返回指定字符的索引。
3. charAt()：返回指定索引处的字符。
4. replace()：字符串替换。
5. trim()：去除字符串两端空白。
6. split()：分割字符串，返回一个分割后的字符串数组。
7. getBytes()：返回字符串的 byte 类型数组。
8. length()：返回字符串长度。
9. toLowerCase()：将字符串转成小写字母。
10. toUpperCase()：将字符串转成大写字符。
11. substring()：截取字符串。
12. equals()：字符串比较。

---

#### 抽象类 抽象方法

1. 普通类不能包含抽象方法，抽象类可以包含抽象方法，当然也可以不包含。
2. 抽象类不能直接实例化，普通类可以直接实例化。
3. final不能修饰抽象类

#### 抽象类 和 接口 比较

1. 语法肯定不同： 类： extends 接口：implement
2. 抽象的东西不同：类： 抽象整个东西，行为，对象  接口：行为
3. 类只可能在优化的时候写，因为有了类才有抽象类

    接口是自上而下的设计思路

#### Override vs Overload

1. Override: 
- 子类对父类的允许访问的方法的实现过程进行重新编写, 返回值和形参都不能改变。即外壳不变，核心重写！
- 不能抛出新的检查异常或者比被重写方法申明更加宽泛的异常
- 参数列表与被重写方法的参数列表必须完全相同。
- 返回类型与被重写方法的返回类型可以不相同，但是必须是父类返回值的派生类（java5 及更早版本返回类型要一样，java7 及更高版本可以不同）。
- 访问权限不能比父类中被重写的方法的访问权限更低。例如：如果父类的一个方法被声明为 public，那么在子类中重写该方法就不能声明为 protected。
- 父类的成员方法只能被它的子类重写。
- 声明为 final 的方法不能被重写。
- 声明为 static 的方法不能被重写，但是能够被再次声明。
- 子类和父类在同一个包中，那么子类可以重写父类所有方法，除了声明为 private 和 final 的方法。
- 子类和父类不在同一个包中，那么子类只能够重写父类的声明为 public 和 protected 的非 final 方法。
- 重写的方法能够抛出任何非强制异常，无论被重写的方法是否抛出异常。但是，重写的方法不能抛出新的强制性异常，或者比被重写方法声明的更广泛的强制性异常，反之则可以。
- 构造方法不能被重写。
- 如果不能继承一个类，则不能重写该类的方法。


2. Overload: 在一个类里面，方法名字相同，而参数不同。返回类型可以相同也可以不同, 常见： 构造器的重载

- 被重载的方法必须改变参数列表(参数个数或类型不一样)；
- 被重载的方法可以改变返回类型；
- 被重载的方法可以改变访问修饰符；
- 被重载的方法可以声明新的或更广的检查异常；
- 方法能够在同一个类中或者在一个子类中被重载。
- 无法以返回值类型作为重载函数的区分标准。


|区别点	|overload	|override|
| --- | --- | ---| 
|参数列表	|必须修改	|一定不能修改|
|返回类型	|可以修改	|一定不能修改|
|异常	|可以修改	|可以减少或删除，一定不能抛出新的或者更广的异常|
|访问	|可以修改	|一定不能做更严格的限制（可以降低限制）|

---

#### HashMap 原理：

HashMap中hash数组的默认大小是16，而且一定是2的指数

Hash table based implementation of the Map interface. This implementation provides all of the optional map operations, and permits null values and the null key. (The HashMap class is roughly equivalent to Hashtable, except that it is unsynchronized and permits nulls.) This class makes no guarantees as to the order of the map; in particular, it does not guarantee that the order will remain constant over time.

1. 基于Map
2. 可以是null key/value
3. 不保证有序， 顺序不变

- configuration
1. Initial capacity The capacity is the number of buckets in the hash table, The initial capacity is simply the capacity at the time the hash table is created.
2. Load factor (0.75) The load factor is a measure of how full the hash table is allowed to get before its capacity is automatically increased. 

如果达到了load factor的话，bucket直接扩大一倍（这部分和go里面的slice一样）

- Put

![](https://user-gold-cdn.xitu.io/2019/12/5/16ed3af3b5ad0137?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

1. key == null --> table[0]
1. 算hash
2. 如果没有碰撞，直接放bucket里面
3. 有碰撞，list
4. list太长的话（8）， tree
5. bucket可能需要resize

- Get
1. hash
2. hash + 链表 --> hash + key (Ologn)
3. hash + tree --> hash + key (On)

- Hash
h ^ (h >> 16)

- Index
(n-1) & hash (n 为 table目前长度 -- 2的幂)

-- > 频繁碰撞 -- tree

---

#### HashTable vs HashMap

- HashTable线程安全 （Synchronized），HashMap线程不安全；
- 初始值和扩容方式不同。HashTable的初始值为11，扩容为原大小的2*d+1。容量大小都采用奇数且为素数，且采用取模法，这种方式散列更均匀。但有个缺点就是对素数取模的性能较低（涉及到除法运算），而HashTable的长度都是2的次幂，设计就较为巧妙， 性能较好。
- HashTable 不能有null

---

#### HashMap 问题：

1. 线程不安全： A、B两个线程同时执行put()操作，且两个key都指向同一个buekct，那么此时两个结点，都会做头插法，数据覆盖
--> 删除操作、修改操作，同样都会有覆盖问题

2. 1.7 扩容死循环 --> 头插入 会把 原数据 反向 e.g. 1->2->3 => 3->2->1 所以说如果两个线程A和B, B因为时间耽搁了，就会形成cycle link 

=> 1.8 后插入 + concurrentHashMap

#### concurrentHashMap

![](https://img-blog.csdn.net/20170818105333565?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcmlja2l5ZWF0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

concurrentHashMap = segment + HashEntry

Segment是一种可重入锁ReentrantLock，在ConcurrentHashMap里扮演锁的角色，HashEntry则用于存储键值对数据。一个ConcurrentHashMap里包含一个Segment数组，Segment的结构和HashMap类似，是一种数组和链表结构， 一个Segment里包含一个HashEntry数组，每个HashEntry是一个链表结构的元素， 每个Segment守护者一个HashEntry数组里的元素,当对HashEntry数组的数据进行修改时，必须首先获得它对应的Segment锁。

- Get: 不加锁
- Put: 加锁， 先看是不是需要扩容
- size: 先不加锁count两次，如果结果不一样的话 就是加锁count

---

#### 内存泄漏

在Java中，内存泄漏就是存在一些被分配的对象，这些对象有下面两个特点：
    
    - 首先，这些对象是可达的，即在有向图中，存在通路可以与其相连；
    
    - 其次，这些对象是无用的，即程序以后不会再使用这些对象。
    如果对象满足这两个条件，这些对象就可以判定为Java中的内存泄漏，这些对象不会被GC所回收，然而它却占用内存。

    常见： List、MAP等集合对象是否有使用完后，未清除的问题。List、MAP等集合对象会始终存有对对象的引用，使得这些对象不能被GC回收

#### List、set、Map

>ArrayList实现原理要点概括

1. ArrayList是List接口的可变数组非同步实现，并允许包括null在内的所有元素。
底层使用数组实现
2. 该集合是可变长度数组，数组扩容时，会将老数组中的元素重新拷贝一份到新的数组中，每次数组容量增长大约是其容量的1.5倍，这种操作的代价很高。
3. 采用了Fail-Fast机制，面对并发的修改时，迭代器很快就会完全失败，而不是冒着在将来某个不确定时间发生任意不确定行为的风险
4. remove方法会让下标到数组末尾的元素向前移动一个单位，并把最后一位的值置空，方便GC

>LinkedList实现原理要点概括

1. LinkedList是List接口的双向链表非同步实现，并允许包括null在内的所有元素。
2. 底层的数据结构是基于双向链表的，该数据结构我们称为节点
3. 双向链表节点对应的类Node的实例，Node中包含成员变量：prev，next，item。其中，prev是该节点的上一个节点，next是该节点的下一个节点，item是该节点所包含的值。
4. 它的查找是分两半查找，先判断index是在链表的哪一半，然后再去对应区域查找，这样最多只要遍历链表的一半节点即可找到

>HashMap实现原理要点概括

1. HashMap是基于哈希表的Map接口的非同步实现，允许使用null值和null键，但不保证映射的顺序。
底层使用数组实现，数组中每一项是个单向链表，即数组和链表的结合体；当链表长度大于一定阈值时，链表转换为红黑树，这样减少链表查询时间。
2. HashMap在底层将key-value当成一个整体进行处理，这个整体就是一个Node对象。HashMap底层采用一个Node[]数组来保存所有的key-value对，当需要存储一个Node对象时，会根据key的hash算法来决定其在数组中的存储位置，在根据equals方法决定其在该数组位置上的链表中的存储位置；当需要取出一个Node时，也会根3. 据key的hash算法找到其在数组中的存储位置，再根据equals方法从该位置上的链表中取出该Node。
HashMap进行数组扩容需要重新计算扩容后每个元素在数组中的位置，很耗性能
4. 采用了Fail-Fast机制，通过一个modCount值记录修改次数，对HashMap内容的修改都将增加这个值。迭代器初始化过程中会将这个值赋给迭代器的expectedModCount，在迭代过程中，判断modCount跟expectedModCount是否相等，如果不相等就表示已经有其他线程修改了Map，马上抛出异常

>Hashtable实现原理要点概括

1. Hashtable是基于哈希表的Map接口的同步实现，不允许使用null值和null键
2. 底层使用数组实现，数组中每一项是个单链表，即数组和链表的结合体
3. Hashtable在底层将key-value当成一个整体进行处理，这个整体就是一个Entry对象。Hashtable底层采用一个Entry[]数组来保存所有的key-value对，当需要存储一个Entry对象时，会根据key的hash算法来决定其在数组中的存储位置，在根据equals方法决定其在该数组位置上的链表中的存储位置；当需要取出一个Entry时，也会根据key的hash算法找到其在数组中的存储位置，再根据equals方法从该位置上的链表中取出该Entry。
4. synchronized是针对整张Hash表的，即每次锁住整张表让线程独占

>ConcurrentHashMap实现原理要点概括

1. ConcurrentHashMap允许多个修改操作并发进行，其关键在于使用了锁分离技术。
2. 它使用了多个锁来控制对hash表的不同段进行的修改，每个段其实就是一个小的hashtable，它们有自己的锁。只要多个并发发生在不同的段上，它们就可以并发进行。
3. ConcurrentHashMap在底层将key-value当成一个整体进行处理，这个整体就是一个Entry对象。Hashtable底层采用一个Entry[]数组来保存所有的key-value对，当需要存储一个Entry对象时，会根据key的hash算法来决定其在数组中的存储位置，在根据equals方法决定其在该数组位置上的链表中的存储位置；当需要取出一个Entry时，也会根据key的hash算法找到其在数组中的存储位置，再根据equals方法从该位置上的链表中取出该Entry。
4. 与HashMap不同的是，ConcurrentHashMap使用多个子Hash表，也就是段(Segment)
5. ConcurrentHashMap完全允许多个读操作并发进行，读操作并不需要加锁。如果使用传统的技术，如HashMap中的实现，如果允许可以在hash链的中间添加或删除元素，读操作不加锁将得到不一致的数据。ConcurrentHashMap实现技术是保证HashEntry几乎是不可变的。

>HashSet实现原理要点概括

1. HashSet由哈希表(实际上是一个HashMap实例)支持，不保证set的迭代顺序，并允许使用null元素。
2. 基于HashMap实现，API也是对HashMap的行为进行了封装，可参考HashMap

>LinkedHashMap实现原理要点概括

1. LinkedHashMap继承于HashMap，底层使用哈希表和双向链表来保存所有元素，并且它是非同步，允许使用null值和null键。
2. 基本操作与父类HashMap相似，通过重写HashMap相关方法，重新定义了数组中保存的元素Entry，来实现自己的链接列表特性。该Entry除了保存当前对象的引用外，还保存了其上一个元素before和下一个元素after的引用，从而构成了双向链接列表。

>LinkedHashSet实现原理要点概括

1. 对于LinkedHashSet而言，它继承与HashSet、又基于LinkedHashMap来实现的。LinkedHashSet底层使用LinkedHashMap来保存所有元素，它继承与HashSet，其所有的方法操作上又与HashSet相同。

---

线程池：

新建线程 -> 达到核心数 -> 加入队列 -> 新建线程（非核心） -> 达到最大数 -> 触发拒绝策略

1. 线程数量未达到corePoolSize，则新建一个线程(核心线程)执行任务
2. 线程数量达到了corePools，则将任务移入队列等待
3. 队列已满，新建线程(非核心线程)执行任务
4. 队列已满，总线程数又达到了maximumPoolSize，就会由(RejectedExecutionHandler)抛出异常

---

四种拒绝策略：

1. AbortPolicy：丢弃任务并抛出RejectedExecutionException异常
2. DiscardPolicy：丢弃任务，但是不抛出异常。
3. DisCardOldSetPolicy：丢弃队列最前面的任务，然后提交新来的任务
4. CallerRunPolicy：由调用线程（提交任务的线程，主线程）处理该任务

---

Heap JVM:

1. Young: Eden, From Survivor, To Survivor
2. Old
