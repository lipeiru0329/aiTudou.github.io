---
title: photo-style
date: 2020-06-17 20:38:24
tags:
---

## Photo Customize Style

这届是多个图片风格的融合。

1. 先random建新的图片
2. cost function
3. 让cost function最少
<!-- More -->

![](/images/photo-style.png)

---

### Const function

J = J(content) + J(style)

J(content) = 0.5 * (a(l)(c) - a(l)(g))^2 
 
 a: activation function

 ![](/images/style-cost.png)

 ---

 ## Conclusion


Neural Style Transfer is an algorithm that given a content image C and a style image S can generate an artistic image
- It uses representations (hidden layer activations) based on a pretrained ConvNet.
- The content cost function is computed using one hidden layer's activations.
- The style cost function for one layer is computed using the Gram matrix of that layer's activations. 
- The overall style cost function is obtained using several hidden layers.
- Optimizing the total cost function results in synthesizing new images.

## Reference

1. Leon A. Gatys, Alexander S. Ecker, Matthias Bethge, (2015). [A Neural Algorithm of Artistic Style](https://arxiv.org/abs/1508.06576)
2. Harish Narayanan, [Convolutional neural networks for artistic style transfer.](https://harishnarayanan.org/writing/artistic-style-transfer/)
3. Log0, [TensorFlow Implementation of "A Neural Algorithm of Artistic Style".](http://www.chioka.in/tensorflow-implementation-neural-algorithm-of-artistic-style)
4. Karen Simonyan and Andrew Zisserman (2015). [Very deep convolutional networks for large-scale image recognition](https://arxiv.org/pdf/1409.1556.pdf)
5. [MatConvNet](http://www.vlfeat.org/matconvnet/pretrained/)