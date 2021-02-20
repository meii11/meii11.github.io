---
title: 'Video Modeling with Correlation Networks'
tags: paper CV Y2021
---
今天读的是一篇发表在 CVPR2020 上的文章，因为自己的实验结果虽然不错，但是缺少相关‘创新点’，还需多多读论文呀，争取之后一个月每两天更新一篇。

## Abstract 

这篇文章主要是关于视频动作识别（Video Action Recognition）的，motion 作为 salient cue 是这类算法的核心关注点。
往常的做法有两种，一种是双流网络，通过两只网络同时处理 RGB 信息和 Optical Flow 信息后再进行融合，已达到获取视频的 motion 信息；第二种就是简单的使用 3D CNN 来获取视频的时空动作信息。

作者则提出一种基于可学习相关算子（correlation operator）的替代方法，用于在网络不同层的卷积特征映射上建立帧与帧的映射关系。作者说这种方法可以将通过 2D CNN 获取的表象信息与明确的时间匹配信息（即上面提到的帧与帧的映射关系）进行融合（也就是提出一种新的双流网络）。

## Related Work

作者的灵感来自于 FlowNet中的相关层（correlation layer），不过 FlowNet 中的相关层只是将 RGB 像素空间的视频信息转换到动作位移空间（motion displacement space）（其实就是通过一个算法将 RGB 视频转换成光流）。而作者提出的可学习的相关算子则是通过卷积特征映射来建立帧与帧之间的匹配，从而在网络的不同层捕获不同的相似概念。

> propose a learnable corrlation opretor to establish frame-to-frame matches over convolutional feature maps to capture **different notions of similarity** in different layers of the network.

传统的双流网络是分别对表象信息和光流信息进行训练，并在网络的最后进行融合（一般是 element-wise multiplicaiton），而作者的模型则可以在整个网络上进行表象和动作信息的融合。

而和 3D CNN 相比，作者模型则是将表象信息和动作信息的计算进行分解（分开来算），并学习了捕获不同补丁相似性度量的、不同的过滤器，这种过滤器可以捕获不同方向上的像素位移。


### Applications of correlation operation

Deep matching 通过计算图像小块（image patches）的相关性，寻找稠密对应，用以改善光流。FlowNet 则是 *performs multiplicative patch comparisons*（这里实在不知道怎么翻译了。。。）

还有很多基于 CNN 的光流网络用到了 correlation layers，作者的模型同样也是，不过和那些明着暗着尝试去评估光流的模型不同，作者模型中的 correlation operator 是在和其他算子进行融合时重复使用的，因此可以同时学习到表象和动作信息。

## Correlation operator

首先介绍两张图片之间的相关算子，之后通过 group 的操作在减少参数量、保证准确度的情况下增加输出通道数。

### Correlation operator for matching

每张图像用 C\*H*W 的形式表示，这里主要是在计算两张图的相似度，分别取图 B 的一个图像小块 $P^B(i, j)$ 以及图 A 的一个图像小块 $P^A(i^\`, j^\`)$ ，其中 i j 代表了空间维度的位置，为了表示方便设定这里图像小块代表一个像素点，则 $P^A(i^\`, j^\`)$ 和 $P^B(i, j)$ 代表了一个 C 长度的一维向量。

则两个图像小块的相似度则被定义为两个向量之间的点积：$$S(i,j,i^\`,j^\`) = 1/C * \sum_{c=1}^C{(P^B_c(i,j) * P^A_c(i^\`, j^\`))} $$