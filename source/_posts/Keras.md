---
title: Keras
date: 2020-06-07 01:11:46
tags:
---

## Keras

1. argmax VS max

```
import numpy as np
vector = np.array([1, 2, 3, 2, 1])
```
Now, np.max(vector) returns the number 3, as this is the maximal value in the vector. np.argmax(vector) however returns 2, as this is the index of the maximal value in the vector.

The argmax function is often used to post-process the output of a softmax layer. Say the output layer of your classifier (which classifies some image into one of four classes) is
```
output = Dense(4, activation='softmax')(...)
```
and the output of predict(some_random_image) is [0.02, 0.90, 0.06, 0.02]. Then, argmax([0.02, 0.90, 0.06, 0.02]) immediately gives you the class (1).

### Argmax

Your data has some shape (19,19,5,80). This means:

Axis 0 = 19 elements
Axis 1 = 19 elements
Axis 2 = 5 elements
Axis 3 = 80 elements
Now, negative numbers work exactly like in python lists, in numpy arrays, etc. Negative numbers represent the inverse order:

Axis -1 = 80 elements
Axis -2 = 5 elements
Axis -3 = 19 elements
Axis -4 = 19 elements
When you pass the axis parameter to the argmax function, the indices returned will be based on this axis. Your results will lose this specific axes, but keep the others.

See what shape argmax will return for each index:

K.argmax(a,axis= 0 or -4) returns (19,5,80) with values from 0 to 18
K.argmax(a,axis= 1 or -3) returns (19,5,80) with values from 0 to 18
K.argmax(a,axis= 2 or -2) returns (19,19,80) with values from 0 to 4
K.argmax(a,axis= 3 or -1) returns (19,19,5) with values from 0 to 79