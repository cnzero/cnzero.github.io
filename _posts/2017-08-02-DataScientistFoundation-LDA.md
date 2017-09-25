---
title: "DataScientistFoundation-LDA"
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

### A glance of [sklearn.discriminant_analysis.LinearDiscriminantAnalysis](http://scikit-learn.org/stable/modules/generated/sklearn.discriminant_analysis.LinearDiscriminantAnalysis.html#sklearn.discriminant_analysis.LinearDiscriminantAnalysis)

#### Why to have a glance of LDA
因为我在自己的工程项目中使用到了LDA这样一个简单的算法，但是每次都不能非常好地模块化使用，这一次决定参考sklearn的写法，使用面向对象的思想，将其中关键的参数、属性与方法操作等进行封装，希望可以写成一个模块；同时也是对sklearn源码的一种学习；更重要的是，基于这种模仿以后能够将更多的算法写成这种模块，更好地在Pipeline替换与测试。

#### Parameters
1. `solver` 求特征值的方法 eig或svd等；(忽略)
2. `shrinkage` （忽略）
3. `priors` 先验概率， （忽略）
4. `n_components` Number of components for dimensionality reduction.
5. `store_convariance` （忽略）

#### Attributes
1. `coef_`
2. `intercept_`
3. `convariance_` 保留 shape(n_features, n_features)
4. `explained_variance_ratio_` shape(n_components, )
5. `means_` shape(n_classes, n_features)
6. `priors_`
7. `scalings_`
8. `xbar_`
9. `classes_`


#### Methods
1. `decision_function(X)`
2. `fit(X, y)`
3. `fit_transform(X)`
4. `get_params()`
5. `predict(X)` predict class labels for samples in X
6. `predict_log_proba(X)`
7. `predict_proba(X)`
8. `score(X, y)`
9. `set_params`
10. `transform(X)`
