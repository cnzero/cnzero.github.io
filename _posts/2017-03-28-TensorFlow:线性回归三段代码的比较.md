---
title: "TensorFlow:线性回归三段代码的比较"
layout: post
date: 2017-03-28 20:48
image: /assets/images/markdown.jpg
headerImage: 
tag:
- TensorFlow
blog: true
author: cnzero
description: Learning notes of Google TensorFlow
---
[TOC]


## 目的
省去必要的头与声明， 对关键部分进行比较与理解性说明。
感觉：
虽然这仅仅是一个线性回归的问题，哪怕是我手算也能得到很好的结果。
可是突然间多出来Tensorflow这样的一个大框架，给出的接口实在太多，一时间应接不暇。


## Step


### 训练样本的准备

##### Basic Usage

```
x = np.array([1., 2., 3., 4.])
y = np.array([0., -1.,-2.,-3.])
input_fn = tf.contrib.learn.io.numpy_input_fn({"x":x}, y, batch_size=4,num_epochs=1000)
```

##### Complete Codes

```
# training date
x_train = [1, 2, 3, 4]
y_train = [0,-1,-2,-3]
```

##### Custom Model


```
x = np.array([1., 2., 3., 4.])
y = np.array([0.,-1.,-2.,-3.])
input_fn = tf.contrib.learn.io.numpy_input_fn({'x':x}, y, 4, num_epochs=1000)
```



### 机器学习的三要素：模型-策略-算法

##### Basic Usage

```
features = [tf.contrib.layers.real_valued_column('x', dimension=1)]
estimator = tf.contrib.learn.LinearRegressor(feature_columns=features)
```

##### Complete Codes

```
linear_model = W*x + b
loss = tf.reduce_sum(tf.square(linear_model - y)) 
optimizer = tf.train.GradientDescentOptimizer(0.01)
train = optimizer.minimize(loss)
```

##### Custrom Model

```
def model(features, labels, mode):
    W = tf.get_variable('W', [1], dtype=tf.float64)
    b = tf.get_variable('b', [1], dtype=tf.float64)
    y = W*features['x'] + b
    loss = tf.reduce_sum(tf.square(y - labels))
    global_step = tf.train.get_global_step()
    optimizer = tf.train.GradientDescentOptimizer(0.01)
    train = tf.group(optimizer.minimize(loss), tf.assign_add(global_step, 1))
    return tf.contrib.learn.ModelFnOps(mode=mode, predictions=y, loss=loss, train_op=train)

estimator = tf.contrib.learn.Estimator(model_fn=model)
```

### 训练过程
##### Basic Usage

```
estimator.fit(input_fn=input_fn, steps=1000)
print(estimator.evaluate(input_fn=input_fn))
```

##### Complete Codes

```
for i in range(1000):
    sess.run(train, {x:x_train, y:y_train})
# parameters' results and error evaluation
result_W, result_b, result_e = sess.run([W, b, loss], {x:x_train, y:y_train})
print('W: %s b: %s loss: %s' %(result_W, result_b, result_e))
```

##### Custom Code

```
estimator.fit(input_fn=input_fn, steps=1000)
print(estimator.evaluate(input_fn=input_fn, steps=10))
```

## Summary
经过上面简单的横向对比，及纵向梳理，就条理了些许。
