---
title: CNN Object Detection
date: 2020-05-28 23:40:00
tags:
disqusId: aitudou-blog
---

## Object Detection:

### Object Location:

for the object location, we are to find 4 parameters. x, y, w, h to draw a rectangle. 

## Window Sliding Detect:

1. this idea use a rectange window to detect whether it is a car, then slide to the right and slide down (similiar to do the CNN Convolution). 
<!-- More -->

![object detection](/images/objectdetection.png)

2. Then change to larger rectangle

...

3. finally, you can find the car. 

但是问题是 computation cost 还有 准确率

### Slide windows Comvolution Implemention

于是我们需要把sliding window 变成 CNN的方式去处理

1. 原图片加Stripe
2. 跟CNN 一样 去 以一个step 去跳转截图
3. 用截的图 去按照正常的流程 5*5 --> 2*2 --> 5*5 > 1*1 > 1*1 

![](/images/CNN_OD.jpg)

### Detection Correction

IOU: Intersection over unit

是表detect的区域a和目标区域b的关系

IoU = (a n b) / (a u b) 普遍来说 IoU 大于0.5 便认为是准确的

## YOLO: You only look once:
相对于R-CNN算法， YOLO 特别快但是准确率第一点

### YOLO_V1

YOLO 基本思路就是把一张图片分成n*n个部分, 每一个部分分别负责检测(有无东西，矩形长, 矩形宽, 矩形中心点的x, 矩形中心点的y, 再加上你想detect的C个分类 -- 一共是5 * B+ C个 -- 所以说y 是一个 [1 * 5 * B + C] 的matrix)

总结一下，每个单元格需要预测 __5 * B+ C__ 个值。如果将输入图片划分为 __S*S__ 网格，那么最终预测值为 __S*S*(5*B+C)__ 大小的张量


### NON-Max suppression algorithm:
就是说只留下概率最大的或者说概率比较大的 剩下的discard 比如说你只留下p > 0.6 的

### Anchor Boxes
anchor boxes 是为了解决之前一个box只能detect一种东西的问题，利用anchor box可以实现一个box中同时detect多个东西

![](/images/anchorBox.jpg)
![](/images/anchorBoxCOmpare.jpg)

目前anchorBox 对三个及以上同时 detect 的效果并不好

## 题目：

![](/images/CNN-OD-Q3.png)

这题是因为易拉罐的长宽已知，所以说只需要x和y就行了

![](/images/CNN-OD-Q4.png)

![](/images/CNN-OD-Q9.png)

![](/iamges/CNN-OD-Q10.png)

这题需要注意： 25 = 1 + 4 + 20
1： pc+ 有没有
4： x，y，w，h
20：20个classes
