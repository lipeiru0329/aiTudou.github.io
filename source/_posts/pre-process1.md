---
title: pre-process1
date: 2020-08-12 14:18:36
tags:
---

## 预处理-1
<!-- More -->

### One-hot

one-hot是用来处理分类的时候的特征值的

很多机器学习任务中，特征并不总是连续值，有可能是分类值。

```
["male", "female"]

["from Europe", "from US", "from Asia"]

["uses Firefox", "uses Chrome", "uses Safari", "uses Internet Explorer"]
```

如果将上述特征用数字表示，效率会高很多。例如：

```
["male", "from US", "uses Internet Explorer"] 表示为[0, 1, 3]

["female", "from Asia", "uses Chrome"]表示为[1, 2, 1]
```

这个时候就是用one-hot的时候了 

可以这样理解，对于每一个特征，如果它有m个可能值，那么经过独热编码后，就变成了m个二元特征。并且，这些特征互斥，每次只有一个激活。因此，数据会变成稀疏的。

这样做的好处主要有：

1. 解决了分类器不好处理属性数据的问题

2. 在一定程度上也起到了扩充特征的作用

举个例子
```
encoder = preprocessing.OneHotEncoder()
encoder.fit([
    [0, 2, 1, 12],
    [1, 3, 5, 3],
    [2, 3, 2, 12],
    [1, 2, 4, 3]
])
encoded_vector = encoder.transform([[2, 3, 5, 3]]).toarray()
print("\n Encoded vector =", encoded_vector)

Encoded vector = [[ 0. 0. 1. 0. 1. 0. 0. 0. 1. 1. 0.]]

```

```
4个特征：
第一个特征（即为第一列）为[0,1,2,1] ，其中三类特征值[0,1,2]，因此One-Hot Code可将[0,1,2]表示为:[100,010,001]
同理第二个特征列可将两类特征值[2,3]表示为[10,01]
第三个特征将4类特征值[1,2,4,5]表示为[1000,0100,0010,0001]
第四个特征将2类特征值[3,12]表示为[10,01]

因此最后可将[2,3,5,3]表示为[0,0,1,0,1,0,0,0,1,1,0]
```
