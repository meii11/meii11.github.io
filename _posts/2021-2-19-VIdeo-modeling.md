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

每张图像用 C\*H\*W 的形式表示，这里主要是在计算两张图的相似度，分别取图 B 的一个图像小块 $P^B(i, j)$ 以及图 A 的一个图像小块 $P^A(i^\`, j^\`)$ ，其中 i j 代表了空间维度的位置，为了表示方便设定这里图像小块代表一个像素点，则 $P^A(i^\`, j^\`)$ 和 $P^B(i, j)$ 代表了一个 C 长度的一维向量，如 Figure 1。

则两个图像小块的相似度则被定义为两个向量之间的点积：$$S(i,j, i^\`, j^\`) = 1/C * \sum_{c=1}^C{(P^B_c(i,j) * P^A_c(i^\`, j^\`))} $$

这里的 1/C 表示正则化，并且 (i^\`, j^\`) 被设定为 (i, j)的大小为 k*k 的相邻节点，其中 k 可以代表图像小块（当然这里是一维向量）的最大位移（displacement），考虑到所有的可能性，则 S 是大小为 K\*K\*H\*W 的一个 tensor(这里本来应该是 C\*K\*K\*H\*W 大小，但是有一个 1/C 的正则化操作，把 C 除掉了，下面的分组相似算子也是同理，只不过那里是 &1/(C/G))&，如果将 K\*K 表示为 $K^2$，则它就可以起到通道的作用，S 就是一个大小为 $K^2\*H\*W$ 的 3D 特征张量了。

![Snipaste_2021-02-20_11-31-59.png](https://i.loli.net/2021/02/20/aonM6XDHgpZ3bUF.png)

### Learnable correlation operator

这里其实就是给每一个 dot 添加了一个权值 $W_c$，因此对于一个 K\*K 大小的区域，权值矩阵（filter）的大小为 C\*K\*K，则这个过程变成了可以学习的。

K 的大小代表了匹配两个图像小块时的最大位移，虽然 K 越大表示的区域越大、能够编码更多的内容，但是会增加计算消耗。因此作者这里引入了类似空洞卷积的 dialate，设定为 K=7 D=2 用于覆盖大小为 13\*13 的像素点。 

作者也采用了在不同空间范围使用算子的方法（这里应该是指不同范围采用不同算子）。

在 K\*K 范围采用不同的权值，可以使得过滤器在不同方向学习到像素的移动。

### Groupwise correlation operator

上面提到的相似算子（correlation operator）将大小 C\*H\*W 的特征图变成 $K^2\*H\*W$ 大小。这个比起传统 CNN 来说小很多很多（one to two orders of magnitude），对于传统使用相似算子的光流算法，因为只用到了一次所以几乎无影响，但是如果要重复利用相似算子来设计网络的话就会削弱网络学习特征的能力（因为通道数减少很多）。因此作者采用了分组版本（groupwise）的相似算子，在保持准确度的同时避免减少通道数。

分组卷积（groupwise convolution）通过将每个核限制在一个子集的特征通道上来减少卷积的计算消耗。分组相关算子也是同理，将图像和过滤器的通道 C 同样分为 G 组，然后在每一组内使用特征算子，最后所有组的输出进行堆叠得到最终结果。根据上面提到的，每个组的输出都是 $K^2\*H\*W$ 大小，则 G 组进行叠加则有 $K^2\*G\*H\*W$ 大小，即通道数变成了 $K^2\*G$ 。

### From two images to a video clip

上面提到的相似算子都是针对两张图片的，将其扩展到由 L 张视频帧序列上，方法就是分别对视频帧中两两相隔的帧计算相似算子，并对第一帧对自己计算相似算子，则得到了长度为 L 的输出。

![Snipaste_2021-02-20_16-33-08.png](https://i.loli.net/2021/02/20/dXy48ehlZGwJWDc.png)

上图就是相似算子和 3D CNN 的相关对比。他这个可以理解为提出了一种新的光流卷积算法？

## Correlation Network

相似算子可以捕获视频帧的时间信息，需要和其他可以捕捉表象信息的算子进行组合，得到一个正常的网络。

作者设计了这么一个 module，将它插入到 backbone 中，作者用的是 R(2+1)D。

![Snipaste_2021-02-20_16-51-23.png](https://i.loli.net/2021/02/20/Ws8Ot5CFv314Vfm.png)


## Result

![Snipaste_2021-02-20_16-53-45.png](https://i.loli.net/2021/02/20/eJ5yL3cCZGhTWju.png)

其实从结果来看提升并不大，在 something-something 下只有 47.4， 我的模型都有 49.6，不过参数量我没有算，之后会补上（碎碎念。。。）