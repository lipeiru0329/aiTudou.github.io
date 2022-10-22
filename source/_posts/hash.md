---
title: hash
date: 2022-10-19 14:34:52
tags:
---

## 一致性

这次讲一致性，一致性是在 多线程的时候很常见的考察点

在多线程的时候 常见考察这些知识。

    1.1 一致性

    当给哈希增加一个新桶时，需要对已有的key进行重新映射。一致性的意思是，在这个重新映射的过程中，key要么保留在原来的桶中，要么移动到新增加的桶中。如果key移动到原有的其他桶中，就不满足“一致性”了。

    这是一致性哈希区别于传统哈希的特征，传统的哈希在增加一个新桶时，一般会对key进行随机重新的随机映射，key很可能移动到其他原有的桶中。

    1.2 均匀性

    均匀性是指key会等概率地映射到每个桶中，不会出现某个桶里有大量key，某个桶里key很少甚至没有key的情况。

    这是一致性哈希和传统哈希都有的特征。

### 方法

1. 基本场景

有 N 个 cache 服务器，如何将一个对象 object 映射到 N 个 cache 上呢，最简单的通用方法，计算 object 的 hash 值，然后均匀的映射到到 N 个 cache

> hash(object)%N

如果坏掉一个节点，cache 就变成 N-1 台，映射关系变成

> hash(object)%(N-1)

如果增加一个节点，cache 就变成了 N+1 台，映射关系变成

> hash(object)%(N+1)

虽然实现很简单，但是不太满足需求：无论增加还是减少，所有的原来 hash 到的节点可能都会发生变化。

2. 经典一致性 hash

![](https://km.woa.com/gkm/api/img/cos-file-url?url=https%3A%2F%2Fkm-pro-1258638997.cos.ap-guangzhou.myqcloud.com%2Ffiles%2Fphotos%2Fpictures%2F%2F20190730%2F%2F1564496165_5.png&is_redirect=1)

每个节点通过 hash 算法（一般可以采用机器的 ip 或者一个唯一的别名）映射到环上，hash(NodeA)，hash(NodeB)，hash(NodeC)，hash(NodeD)
同理，把 4 个对象也通过一定的 hash 算法映射到环上，Hash(object1) ,Hash(object2), Hash(object3)，Hash(object4)。
按照顺时针方向，根据 hash(object)的结果去寻找距离自己最近的节点。
如果节点增加了，增加了一个 NodeE，那么 hash(NodeE)之后，会在环上有一个属于自己的位置，然后按照上边的逻辑看一下，有哪些节点发生了变化呢？object 从 NodeC->NodeX
如果节点减少了，NodeD 被移除，那么沿着 NodeD 逆时针方向的节点 ojbectD 会受到影响，objectD 会映射到 NodeD 的顺时针方向的下一个节点，即 objectA 所在的节点。

#### 问题：

    1. 节点数量比较少的时候，很可能由于 key 的不均衡造成分布不均衡。
    2. 节点数量减少时，容易造成雪崩。

![](https://km.woa.com/files/photos/pictures//20190730//1564496377_35.png)

3. 虚拟节点

为了解决上边的问题，引入了虚拟节点：

![](https://km.woa.com/gkm/api/img/cos-file-url?url=https%3A%2F%2Fkm-pro-1258638997.cos.ap-guangzhou.myqcloud.com%2Ffiles%2Fphotos%2Fpictures%2F%2F20190730%2F%2F1564496447_27.png&is_redirect=1)

[ 带虚拟节点的环 ]

#### 问题：

    1. 一个真实节点应该映射成多少个虚拟节点比较好
    2. 根据虚拟节点如何找到对应的真实节点

3. Ketama 一致性 Hash 算法

ketama 一致性 hash 是带虚拟节点的一致性 hash 算法的一种实现，算法大致过程如下：

    1. 服务器列表会配置到一个文件中
    (eg: 1.2.3.4:11211 100, 5.6.7.8:11211 100, 9.8.7.6:11211 100)
    2. 把每个字符串的服务器地址 hash 成 unsigned int
    3. 把上面的整数放到 0~232 的区间中，形成一个环，叫 continuum
    4. 用一个数据结构，把 hash 的整数和 ip 一一对应起来
    5. 当需要确定某一个 key 的位置时，首先对 key 进行 hash，会得到另一个 unsigned int un，然后在环 continuum 上查找大于 un 的下一个数值。若找到，就把 key 保存到这台服务器上
    6. 如果 un 很大，接近了 232，那么把这个 key 映射到第一个节点上

4. jump consistent hash

直接使用这个递推公式。开始桶的总数为 1，所有的 key 都放在第 0 个桶中。然后每增加一个桶，生成一个随机数，当这个随机数为奇数时，将 key 放在保持在原始桶中，当这个 key 为偶数时，将 key 移动到新增的桶中， 假设当前桶数为 k，如果新增加一个桶，key 移动到新桶的概率为 1/(k+1)，那么算法就可以满足“均匀性”了

首先对 n=1、2、3 这个特殊情况进行推导：

首先，当桶总数 n=1 时，key 分配到第 0 个桶中的概率是 1
新增一个桶，此时 n=2，key 被分配到新桶（第 1 个桶）中的概率是 1/2，保留在原桶中的概率也是 1/2
再新增一个桶，此时 n=3，key 被分配到新桶（第 2 个桶）中的概率是 1/3，保留原桶（第 0 或 1 个桶）中的概率是 1/2 \* 2/3 = 1/3
然后我们可以有更通用的推导：

当 n=k 时，key 被分配到每个桶中的概率是 1/n
再新增一个桶，此时 n=k+1，key 被分配到新桶（第 k 个桶）中的概率是 1/(k+1)，保留原桶（第 0 或 1 或……或 k-1 个桶）中的概率是 1/k \* k/(k+1) = 1/(k+1)。此时 key 被分配到每个桶的概率仍然为 1/n

```
int ch(int key, int n)
{
    random.seed(key);
    int id = 0;
    for (int j = 1; j < n; j++)
    {
        if (random.next() < 1.0/(j+1))
        {
            id = j;
        }
    }
    return id;
}
```
