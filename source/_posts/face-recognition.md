---
title: face-recognition
date: 2020-06-07 16:56:33
tags:
---

## Face-Recognition

这节课是人脸识别

<!-- More -->

难点： 整个系统面临着training set不足的情况，所以说不能用基本的CNN-> softmax 做

--> Similiarity function

![](/images/Face-recognition.png)

### Siamese Network

找两张图片的区别||f(x) - f(x)||^2

### Triplet Loss

every time, training target picture, a positive (同一个人) and negative (不同人的) picture. 
```
|| f(x) - f(p) || ^2 - || f(x) - f(n) || ^2 + a (margin) <= 0

margin: 为了避免出现f(x) = 0
```

![](/images/Triplet-Loss.png)

于是说下一个问题就是怎么悬着 training set

- random: 问题是random different people diff most of time is larger than same people. 

--> FaceNet
<!-- 
### FaceNet

这个网络是为了解决face recognition而设立的 -->

![](/images/faceQ1.png)
![](/images/faceQ2.png)

