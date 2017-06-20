---
title: "TensorFlow-get started"
layout: post
date: 2017-03-21 20:48
image: /assets/images/markdown.jpg
headerImage: false
tag:
- TensorFlow
blog: true
author: cnzero
description: Learning notes of Google TensorFlow
---

[TOC]

### 前言
该笔记全部来源于Google Tensorflow的官方教程 [Getting Started With TensorFlow](https://www.tensorflow.org/get_started/get_started) ， 并有二次加工的嫌疑。

### Basis
#### Tensor
是从基本的矩阵概念向上抽象一层。

一定程度上能否看成MATLAB的高级版呢，这算是一个高级的玩笑吧。

```

# a rank 0 tensor; this is a scalar with shape []
[1. ,2., 3.] # a rank 1 tensor; this is a vector with shape [3]
[[1., 2., 3.], [4., 5., 6.]] # a rank 2 tensor; a matrix with shape [2, 3]
[[[1., 2., 3.]], [[7., 8., 9.]]] # a rank 3 tensor with shape [2, 1, 3]

```
#### Computational Graph
1. two discretes sections of TensorFlow Core
    1. Building the computational graph;
    2. running the computational graph;

#### Node
1.  __computational graph__ is a series of TensorFlow operations arranged into a graph of  _nodes_
2. __nodes__ 多半是借鉴了神经网络中神经元的概念，其特点是 _张量作为输入，张量作为输出_ 。
    _constant nodes_ 就是无输入，则输出为node内部存储的变量。
    _general nodes_ 应该很容易去猜想，其实就是一种 __运算节点__ 或者看成一种函数句柄。
3. _nodes_ 的创建，并观察其内容

```

import os
os.environ['TF_CPP_MIN_LOG_LEVEL'] = '3'
import tensorflow as tf

node1 = tf.constant(3.0, tf.float32)
node2 = tf.constant(4.0)
print(node1, node2)
# Results
Tensor("Const:0", shape=(), dtype=float32) Tensor("Const_1:0", shape=(), dtype=float32)

# general nodes creation
node3 = tf.add(node1, node2)
print('node3:', node3)
print('sess.run(node3):', sess.run(node3))
# Results
node3: Tensor("Add:0", shape=(), dtype=float32)
sess.run(node3): 7.0

```
_little tips_ : `sess.run` 的入口参数必须是一个张量tensor，如果想输出两个nodes的结果的话，不能将两个node直接塞进去 `sess.run(node1, node2)` 而是将两个张量nodes合成一个新的张量 `sess.run([node1, node2])`  注意其中中括号的使用。

#### Session
这个概念暂时还没有完全理解，先给自己挖一个坑。

#### Variable
> Variables allow us to add trianable parameters to a graph. They are constructed with a type and initial value.

```

# Variable
W = tf.Variable([.3], tf.float32)
b = tf.Variable([-.3], tf.float32)
# fixing W-b manually with new assignments.
fixW = tf.assign(W, [-1.])
fixb = tf.assign(b, [1.])
sess.run([fixW, fixb])

```

### An Example - 线性回归

#### Learning Notes
1.确定基本的线性模型 `model = W*x + b` ，作为一种形式。 对其进行初始化
﻿    特别注意:
>To initialize all the variables in a TensorFlow program, you must explicitly call a special operation as following 5th-code.

```

W = tf.Variable([.3], tf.float32)
b = tf.Variable([-.3], tf.float32)
x = tf.placeholder(tf.float32)
linear_model = W*x + b
sess.run(tf.global_variables_initializer)
print(sess.run(linear_model, {x:[1,2,3,4]})) # 在模型中，若确定x序列，确定模型对应的输出
# Result：[ 0.          0.30000001  0.60000002  0.90000004]

```

2.如何定义，并计算模型的损失函数 `loss function` 作为ML中的基本概念，不再多解释

```

y = tf.placeholder(tf.float32)
loss = tf.reduce_sum(tf.square(linear_model - y))
print(sess.run(loss, {x:[1,2,3,4], y:[0,-1,-2,-3]})) # 23.66

```

3.但是回头想想，这样应该不对啊，对于线性模型中W与b而言，应该是可调的，我们的目标就是 ： _在当前的loss的function的优化目标下，找到最佳的W和b_ 。这里首先介绍一种手动调参W和b的方法

```

# fixing W-b manually with new assignments.
fixW = tf.assign(W, [-1.])
fixb = tf.assign(b, [1.])
sess.run([fixW, fixb])
print(sess.run(loss, {x:[1,2,3,4], y:[0,-1,-2,-3]})) # 0.0

```

4.而实际、正确的思路：
    在假设模型与loss function的情况下，利用已经确定的x和y数据集，__训练__ 得到最合适的模型参数W和b。

```

optimizer = tf.train.GradientDescentOptimizer(0.01) # 迭代算法：梯队下降法
train = optimizer.minimize(loss) # 优化策略：最小化loss function
sess.run(tf.global_variables_initializer()) # 重新对变量进行初始化
for i in range(10000):
    sess.run(train, {x:[1,2,3,4], y:[0, -1, -2, -3]}) # 迭代训练
print(sess.run([W, b]))

```

##### 小结：

> TensorFlow provides higher level abstractions for common patterns, structures, and functionality.



#### Complete Codes
经过上述亦步亦趋的教程跟进，并梳理了思路，将教程中代码梳理如下：

```

import os
os.environ['TF_CPP_MIN_LOG_LEVEL'] = '3'
import tensorflow as tf
import numpy as np
# Input and output
x = tf.placeholder(tf.float32)
y = tf.placeholder(tf.float32)
# Model parameters initialization
W = tf.Variable([.3], tf.float32)
b = tf.Variable([.3], tf.float32)
#  -- three key elements of Machine Learning
# Model
linear_model = W*x + b
# Strategy
loss = tf.reduce_sum(tf.square(linear_model - y)) 
# Algorithm
optimizer = tf.train.GradientDescentOptimizer(0.01)
train = optimizer.minimize(loss)

# training date
x_train = [1, 2, 3, 4]
y_train = [0,-1,-2,-3]
sess = tf.Session()

# parameters initialization
sess.run(tf.global_variables_initializer())

# training processing
for i in range(1000):
    sess.run(train, {x:x_train, y:y_train})
# parameters' results and error evaluation
result_W, result_b, result_e = sess.run([W, b, loss], {x:x_train, y:y_train})
print('W: %s b: %s loss: %s' %(result_W, result_b, result_e))

```