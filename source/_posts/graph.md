---
title: graph
date: 2020-12-14 23:40:05
tags:
---


## 图论一：

图是一种复杂的非线性结构。

在线性结构中，数据元素之间满足唯一的线性关系，每个数据元素(除第一个和最后一个外)只有一个直接前趋和一个直接后继；

在树形结构中，数据元素之间有着明显的层次关系，并且每个数据元素只与上一层中的一个元素(双亲节点)及下一层的多个元素(孩子节点)相关；

而在图形结构中，节点之间的关系是任意的，图中任意两个数据元素之间都有可能相关。

图G由两个集合V(顶点Vertex)和E(边Edge)组成，定义为G=(V，E)

### 有向图 无向图

> 无向图是用小括号，而下面介绍的有向图是用尖括号。

对于一个图，若每条边都是没有方向的，则称该图为无向图。图示如下：

![](https://images0.cnblogs.com/blog/311549/201309/23224321-acabc2d2b2414039b8f28b3c73736377.jpg)

对于一个图G，若每条边都是有方向的，则称该图为有向图。图示如下。

![](https://images0.cnblogs.com/blog/311549/201309/23224323-05a9dbe615be404fb1bec89e78d902ea.jpg)

> 我们将具有n(n-1)/2条边的无向图称为无向完全图。同理，将具有n(n-1)条边的有向图称为有向完全图。

### 顶点的度

- 对于无向图，顶点的度表示以该顶点作为一个端点的边的数目。比如，图(a)无向图中顶点V3的度D(V3)=3

- 对于有向图，顶点的度分为入度和出度。入度表示以该顶点为终点的入边数目，出度是以该顶点为起点的出边数目，该顶点的度等于其入度和出度之和。比如，顶点V1的入度ID(V1)=1，出度OD(V1)=2，所以D(V1)=ID(V1)+OD(V1)=1+2=3

### 连通图(无向图)

连通图是指图G中任意两个顶点Vi和Vj都连通，则称为连通图

### 网

带”权值”的连通图称为网。如图所示。

![](https://images0.cnblogs.com/blog/311549/201309/23224329-5c5bb81883bf49ca8f4f030166823dce.jpg)

### 表示方法：

1. [邻接矩阵](https://blog.csdn.net/jnu_simba/article/details/8866705)

2. 邻接表

### 遍历：

DFS, BFS

### 最小生成树

生成树是将图中所有顶点以最少的边连通的子图

权值和最小的生成树就是最小生成树

### 最短路径

求最短路径也就是求最短路径长度。

最短路径问题---Dijkstra算法详解

- 算法的思路

Dijkstra算法采用的是一种贪心的策略，声明一个数组dis来保存源点到各个顶点的最短距离和一个保存已经找到了最短路径的顶点的集合：T，初始时，原点 s 的路径权重被赋为 0 （dis[s] = 0）。若对于顶点 s 存在能直接到达的边（s,m），则把dis[m]设为w（s, m）,同时把所有其他（s不能直接到达的）顶点的路径长度设为无穷大。初始时，集合T只有顶点s。
然后，从dis数组选择最小值，则该值就是源点s到该值对应的顶点的最短路径，并且把该点加入到T中，OK，此时完成一个顶点，
然后，我们需要看看新加入的顶点是否可以到达其他顶点并且看看通过该顶点到达其他点的路径长度是否比源点直接到达短，如果是，那么就替换这些顶点在dis中的值。
然后，又从dis中找出最小值，重复上述动作，直到T中包含了图的所有顶点。

> 下面我求下图，从顶点v1到其他各个顶点的最短路径

![](https://img-blog.csdn.net/20170308144724663?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzU2NDQyMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

1. 首先第一步，我们先声明一个dis数组，该数组初始化的值为：

![](https://img-blog.csdn.net/20170308150247263?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzU2NDQyMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

2. 既然是求 v1顶点到其余各个顶点的最短路程，那就先找一个离 1 号顶点最近的顶点。通过数组 dis 可知当前离v1顶点最近是 v3顶点。当选择了 2 号顶点后，dis[2]（下标从0开始）的值就已经从“估计值”变为了“确定值”，即 v1顶点到 v3顶点的最短路程就是当前 dis[2]值。将V3加入到T中。
为什么呢？因为目前离 v1顶点最近的是 v3顶点，并且这个图所有的边都是正数，那么肯定不可能通过第三个顶点中转，使得 v1顶点到 v3顶点的路程进一步缩短了。因为 v1顶点到其它顶点的路程肯定没有 v1到 v3顶点短.

    OK，既然确定了一个顶点的最短路径，下面我们就要根据这个新入的顶点V3会有出度，发现以v3 为弧尾的有： < v3,v4 >,那么我们看看路径：v1–v3–v4的长度是否比v1–v4短，其实这个已经是很明显的了，因为dis[3]代表的就是v1–v4的长度为无穷大，而v1–v3–v4的长度为：10+50=60，所以更新dis[3]的值,得到如下结果：

![](https://img-blog.csdn.net/20170308150707766?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzU2NDQyMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

然后，我们又从除dis[2]和dis[0]外的其他值中寻找最小值，发现dis[4]的值最小，通过之前是解释的原理，可以知道v1到v5的最短距离就是dis[4]的值，然后，我们把v5加入到集合T中，然后，考虑v5的出度是否会影响我们的数组dis的值，v5有两条出度：< v5,v4>和 < v5,v6>,然后我们发现：v1–v5–v4的长度为：50，而dis[3]的值为60，所以我们要更新dis[3]的值.另外，v1-v5-v6的长度为：90，而dis[5]为100，所以我们需要更新dis[5]的值。更新后的dis数组如下图：

![](https://img-blog.csdn.net/20171205193212203?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzU2NDQyMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

然后，继续从dis中选择未确定的顶点的值中选择一个最小的值，发现dis[3]的值是最小的，所以把v4加入到集合T中，此时集合T={v1,v3,v5,v4},然后，考虑v4的出度是否会影响我们的数组dis的值，v4有一条出度：< v4,v6>,然后我们发现：v1–v5–v4–v6的长度为：60，而dis[5]的值为90，所以我们要更新dis[5]的值，更新后的dis数组如下图：

![](https://img-blog.csdn.net/20170308151732132?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzU2NDQyMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

然后，我们使用同样原理，分别确定了v6和v2的最短路径，最后dis的数组的值如下：

![](https://img-blog.csdn.net/20170308152038851?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzU2NDQyMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

因此，从图中，我们可以发现v1-v2的值为：∞，代表没有路径从v1到达v2。所以我们得到的最后的结果为：

```
起点  终点    最短路径    长度
v1    v2     无          ∞    
      v3     {v1,v3}    10
      v4     {v1,v5,v4}  50
      v5     {v1,v5}    30
      v6     {v1，v5,v4,v6} 60

```
