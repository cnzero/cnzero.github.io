---
title: "DataScientistFoundation-PCA"
layout: post
date: 2017-03-21 20:48
image: /assets/images/markdown.jpg
headerImage: false
tag:
- DSF
- Classifier
projects: true
author: cnzero
description: Foundations of A Data Scientist
---

[TOC]

本文内容框架与撰写思路如下：

###  开胃小菜
[一只兔子帮你理解kNN](https://zhuanlan.zhihu.com/p/22345658?refer=hgjjsms) 其中 以一只兔子作为故事主线，生动活泼，无需一行代码，装配精美的图示，kNN算法中的许多关键点跃然纸上；甚至，仅仅该文章的上来引用`Jim Rohn`的哲理，一下子就将kNN的精髓点透了。文章很赞，是值得学习的榜样。

## 直接上代码吧
[sklearn.neighbors.KNeighborsClassifier Documentation](http://scikit-learn.org/stable/modules/generated/sklearn.neighbors.KNeighborsClassifier.html)


### Parameters:
1. `n_neighbors` kNN 模型的关键也几乎是唯一参数了；
2. `weights`  在计算距离时，对各个维度具有不同的权重设置；
3. `algorithm` kNN虽然思路很简单，但是实现起来容易带来计算量的大问题，针对此分别有许多优化算法，比如kd树等；
4. `leaf_size`，对kd树等算法的设置；
5. `metric`究竟使用哪种距离计算公式；
6. `p` 距离计算的相关参数 p-norm
7. `metric_params` 返回一个dictionary
8. `n_jobs` 因为其中涉及大量的距离运算，实际上并不强调先后顺序，所以，可以使用`并行运算`

### Methods
1. `fit(X,y)` 作为有监督的分类模型，`fit the model using X as training data and y as target values`。 注意 `X` 的维度`[n_samples, n_features]`， `y`的维度`[n_samples, n_output]`
2. `get_params` `Get parameters for this estimator` 无需赘述；
3. `kneighbors(sample=None, n_neighbors=None, return_distance=True)` 样本点`sample`到原训练样本矩阵`X`中最近`n_neighbors`个点，并返回到最近`n_neighbors`个样本点的距离，以及这些样本点在原样本矩阵中的索引index。
