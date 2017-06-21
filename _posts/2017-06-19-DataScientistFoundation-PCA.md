---
title: "DataScientistFoundation-PCA"
layout: post
date: 2017-03-21 20:48
image: /assets/images/markdown.jpg
headerImage: false
tag:
- DSF
projects: true
author: cnzero
description: Foundations of A Data Scientist
---

[TOC]

### 为什么需要进行降维？
现在大家做研究或讨论，往往都已经涉及“大数据”了——该专有名词具有多层含义，此处并不打算进行可以区分，只是说明“数据量变多了”。数据量变多，就难免会出现两个问题：__噪声引入__ 和 __数据重复__ 。

1. 噪声引入:

  ![](/assets/images/PCA/Signal2NoiseVariance.jpg)

   比如，我们通过摄像头记录下“弹簧振子”系统中弹簧末端的运动轨迹；不难发现，方差较小的方向上可以被认为是由于系统误差、测量误差等引入的波动。较小方差方向的量就可以被看做是“噪声”，这可以看做是一种比较合理的假设

   ——对的，这是假设，所以就一定也存在其不合理之处，不合理之处就会导致算法失效。我认为应该是一贯的思维意识，没有什么算法放之四海而皆准，想用，就得首先明白算法的假设及其局限性。

   此时，确实想起来一个更有意思的例子，之前在与人讨论时，曾被用来质疑该降维思路的有效性。

   比如，为了区分男生和女生，大家想出了许多判别特征，包括身高、体重、成绩、头发长短，是否戴眼镜（0和1表示）以及头发数目的多少（对的，你真的没有听错，虽然确实相当浪费计算资源，甚至想一想也没有多大意义，但是我们在机器学习过程中进行特征提取时，又何尝没有犯过这样的错误呢）。

   但最后发现，一群人当中头发数目的方差好大，有上述思想断定“头发数目”是一个较好特征，必须予以保留。但，再想想，也只能呵呵了。
2. 数据重复：

  ![](/assets/images/PCA/Redundancy.jpg)

  举个简单的例子，想量化一群人的特征，大家想出了很多办法和角度，比如身高、体重、性别等。但是呢，针对身高这一特征，有人用单位“米”表示，有人用“英寸”表示，然后都各执一词，将数据记录在表格里面。

  但是，很显然啊，这两组数据就存在了重复——虽然使用了不同的单位，但是两者表示同一个特征。那如何判别呢，两组数据做一个相关性分析，很明显上面的例子会分析得到其相关系数为“1”。虽然记录了很多数据，但是并没有提供额外的信息量，这就是冗余redundancy。所以啊，没有提供额外信息量的数据，我们大可以放心地扔掉，也就是进行“降维”处理。

  这又是一个假设，但显得更合理一些。此时，就有人在想：虽然两者可能不存在较大的相关性，那说不定存在“二次关系”或更复杂的非线性关系呢。是的，但研究起来就更难了。至少此时我们“扔掉”那些具有较大相关性的变量（之一）是非常合理的。

### PCA 作为一种降维方法
O.K. 针对上面所提到的两个问题：噪声引入和数据重复，我们分别提供相应的解决策略。
1. 保留具有较大方差的方向，那就计算各个变量的方差；
2. 消除两两具有较大相关性的变量之一，那就计算两两之间的相关系数；

而这两个东西，恰恰都存在于一个变量里面：协方差矩阵 <img src="http://chart.googleapis.com/chart?cht=tx&chl=\Large  C_{X}
" style="border:none;"> 。 好，那就看看该变量的计算公式：

<img src="http://chart.googleapis.com/chart?cht=tx&chl=\Large  C_{X} \equiv \frac{1}{n} X X^{T} ">

  其中
<img src="http://chart.googleapis.com/chart?cht=tx&chl=\Large  X ">
表示为得到的
<img src="http://chart.googleapis.com/chart?cht=tx&chl=\Large m\times n ">
数据集， 其任意一行表示某特征的所有数据，任意一列表示每次测试中所有的特征，即共有
<img src="http://chart.googleapis.com/chart?cht=tx&chl=\Large n ">
个样本，每个样本含有
<img src="http://chart.googleapis.com/chart?cht=tx&chl=\Large m">
个特征。所以协方差矩阵就是
<img src="http://chart.googleapis.com/chart?cht=tx&chl=\Large m\times m "> 维度。

### PCA的解

### 更多降维方法
