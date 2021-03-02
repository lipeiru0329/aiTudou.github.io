---
title: RNN
date: 2020-06-23 23:41:40
tags:
---

## RNN

RNN Recurrent neural network is one of the most widely used network for the sequence input. 

like this

![](/images/rnn.png)

But the above network"""","""" the most disadvantage is that it only considers the words appearing before. --> Bi-RNN

![](/images/RNNs.png)

---

### RNN 步骤

1. tokenization： 提取单词 直到 EOS （end of sentence）
新单词 --> UNK (unknown)

2. Training: 

    RNN 本质来说 是根据现在的单词，加上之前的单词，推测现在 应该是什么单词，所以说是一个大量的 sotfmax
