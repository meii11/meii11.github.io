---
title: 'Some Paper about Multimodel'
tags: paper MULTIMODEL Y2021
---
最近要做的项目是基于跨模态检索(Cross-modal Retrieval)，于是这次首先介绍一篇综述类文章，后介绍两篇比较相关的论文。
> It takes one type of data as the query to retrieve relevant data to another.

## A comprehensive Survey on Cross-modal Retrieval

### Introduction

多模态数据(multi-modal data)有着异构的属性(heterogeneous property)。对于单模态有着许多排列和寻找技术，但是随着科技的进步，这样的单模态检索并不能满足需求，比如你在长城上游玩，通过拍照上传到社交媒体，你可能会希望使用这张照片来检索相关的文本材料，因此跨模态检索的技术便越来越重要。

跨模态检索的目标是利用一种类型的数据作为一种查询(query)来检索另一种类型的数据中的相似数据。如图所示
![Snipaste_2021-05-26_15-45-17.png](https://i.loli.net/2021/05/26/vHB6UwJFihbmZlr.png)
正在使用文本来检索相关的图像。而且，当使用者通过提交任意媒体类型的数据来查询时，他们可以获得跨越多种类型模态的各种搜寻结果。鉴于不同模态的信息可以提供互补(complementary)的信息，因此这种方法往往更加全面(comprehensive)。

跨模态检索目前面对的挑战是如何测量不同模态数据之间的内容相似度(content similarity)，也成为异构间距(heterogeneity gap)。目前主要的研究目标就是设计一种有效的方法，使得跨模态检索更加精准和可放缩的(scalable)。

### Overview

跨模态检索的过程大致可以分为三步：
+ 对多模态数据进行特征提取(feature extraction)
+ 基于上面提取到的多模态数据特征(representaion)，使用跨模态相关建模(cross-modal correlation modeling)可以学习到不同模态数据的通用表征(common representaion)
+ 最后，通用表征可以通过搜索结果排名和汇总的合适解决方案来进行跨模态检索

目前的跨模态检索的方法可以大致分为两种类型：真值表征学习(real-valued representation learning)和双值表征学习(binary representation learning)，也叫做跨模态哈希(cross-modal hashing)。

对于真值表征学习，学习到的不同模态数据的通用表征是真值。为了加速检索过程，双值表征方法目标是将不同模态数据转换到一个共同的哈希空间，在这个空间内进行跨模态相似性检索十分的快，但是由于表征特征被编码为二进制代码，因此由于信息的丢失，检索精度往往会有所下降。

根据在学习通用表征时的信息利用方式，跨模态检索还可以进一步划分为无监督学习、基于成对的方法(query-based)、基于排名的方法(rank-based)以及监督学习。当然通常来讲，一种方法使用到的信息越多，它的表现往往更好。

## Cross-Modal Interaction Networks for Query-Based Moment Retrieval in Videos




