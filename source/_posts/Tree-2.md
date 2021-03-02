---
title: Tree-2
date: 2020-06-05 14:10:36
tags:
disqusId: aitudou-blog
---

## Tree-2

### B Tree

B 树就是常说的“B 减树（B- 树）”，又名平衡多路（即不止两个子树）查找树，它和平衡二叉树的不同有这么几点：

平衡二叉树节点最多有两个子树，而 B 树每个节点可以有多个子树，M 阶 B 树表示该树每个节点最多有 M 个子树
平衡二叉树每个节点只有一个数据和两个指向孩子的指针，而 B 树每个中间节点有 k-1 个关键字（可以理解为数据）和 k 个子树（ **k 介于阶数 M 和 M/2 之间，M/2 向上取整）
B 树的所有叶子节点都在同一层，并且叶子节点只有关键字，指向孩子的指针为 null

<!-- More -->

![](https://user-gold-cdn.xitu.io/2018/5/29/163a9a83c2c6d53b?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

![](https://user-gold-cdn.xitu.io/2018/5/29/163a9a83c2bc8726?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

### B Tree的平衡

B 树的平衡条件则有三点：

- 叶子节点都在同一层
- 每个节点的关键字数为子树个数减一（子树个数 k 介于树的阶 M 和它的二分之一
- 子树的关键字保证左小右大的顺序

也就是说，一棵 3 阶的 B 树（即节点最多有三个子树），每个节点的关键字数最少为 1，最多为 2，如果要添加数据的子树的关键字数已经是最多，就需要拆分节点，调整树的结构。

![](https://user-gold-cdn.xitu.io/2018/5/29/163a9a83c2de95ba?imageslim)

文件系统和数据库系统中常用的B/B+ 树，他通过对每个节点存储个数的扩展，使得对连续的数据能够进行较快的定位和访问，能够有效减少查找时间，提高存储的空间局部性从而减少IO操作。他广泛用于文件系统及数据库中，如：

Windows：HPFS 文件系统

Mac：HFS，HFS+ 文件系统

Linux：ResiserFS，XFS，Ext3FS，JFS 文件系统

数据库：ORACLE，MYSQL，SQLSERVER 等中

数据库：ORACLE，MYSQL，SQLSERVER 等等

---

## B+ Tree

- 节点的子树数和关键字数相同（B 树是关键字数比子树数少一）
- 节点的关键字表示的是子树中的最大数，在子树中同样含有这个数据
- 叶子节点包含了全部数据，同时符合左小右大的顺序

由于 B+ 树的中间节点不含有实际数据，只有子树的最大数据和子树指针，因此磁盘页中可以容纳更多节点元素，也就是说同样数据情况下，B+ 树会 B 树更加“矮胖”，因此查询效率更快。

B+ 树的查找必会查到叶子节点，更加稳定。

有时候需要查询某个范围内的数据，由于 B+ 树的叶子节点是一个有序链表，只需在叶子节点上遍历即可，不用像 B 树那样挨个中序遍历比较大小。

B+ 树的三个优点：

1. 层级更低，IO 次数更少
2. 每次都需要查询到叶子节点，查询性能稳定
3. 叶子节点形成有序链表，范围查询方便

__注意__ : B Tree 和 B+ Tree 拆分的时候 都是中间或者中间向上取整 只是B Tree拿出来 而B+ Tree 直接拆分

https://www.cs.usfca.edu/~galles/visualization/BPlusTree.html

---

## Java 实现

从上述概念可以得知，树是一个递归的概念，从根节点开始，每个节点至多只有一个父节点，有多个子节点，每个子节点又是一棵树，以此递归。

树有两种实现方式：

- 数组
- 链表

### 数组表示：
```
public class TreeNode {

    private Object mData;   //存储的数据
    private int mParent;   //父亲节点的下标

    public TreeNode(Object data, int parent) {
        mData = data;
        mParent = parent;
    }

    public Object getData() {
        return mData;
    }

    public void setData(Object data) {
        mData = data;
    }

    public int getParent() {
        return mParent;
    }

    public void setParent(int parent) {
        mParent = parent;
    }
}
```

```
public static void main(String[] args){
    TreeNode[] arrayTree = new TreeNode[10];
}
```
![](https://img-blog.csdn.net/20161112192748032)
![](https://img-blog.csdn.net/20161112223704256)

### 链表表示的节点：

```
public class LinkedTreeNode {

    private Object mData;   //存储的数据
    private LinkedTreeNode mParent;   //父亲节点的下标
    private List<LinkedTreeNode> mChildNodeList;    //孩子节点的引用

    public LinkedTreeNode(Object data, LinkedTreeNode parent) {
        mData = data;
        mParent = parent;
    }

    public Object getData() {
        return mData;
    }

    public void setData(Object data) {
        mData = data;
    }

    public Object getParent() {
        return mParent;
    }

    public void setParent(LinkedTreeNode parent) {
        mParent = parent;
    }

    public List<LinkedTreeNode> getChild() {
        return mChildNodeList;
    }

    public void setChild(List<LinkedTreeNode> childList) {
        mChildNodeList = childList;
    }

}
```
```
public static void main(String[] args){
    LinkedList<LinkedTreeNode> linkedTree = new LinkedList<>();
}
```
这样只需知道 根节点就可以遍历整个树。知道某个节点也可以获取它的父亲和孩子。