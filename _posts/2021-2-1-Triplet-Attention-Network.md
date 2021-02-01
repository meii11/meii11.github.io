---
title: 'Rotate to Attend: Convolutional Triplet Attention Module'
tags: -paper 
-video understanding 
-2021
---
> Rotate to Attend: Convolutional Triplet Attention Module

这是一篇发表在 WACV2021 上的文章，初读下来其实就是作者想方设法地想将 channels 和其他通道（spatial location ~~or temporal channel~~）进行融合来使得信息进行交互、相融。

## Abstract

之前的图像领域 cv 算法已相当饱和了，通过在 spatial location 或是 channels 进行单独 attention 操作，使得准确度又有了一波提升。那么作者就突发奇想了，我能不能做一种 cross-dimension interaction，通过这种跨纬度的 attention 使得模型更优化呢？

对于一个输入，首先通过 risidual transformation 操作使其进行旋转（其实就是一个 permute），然后通过卷积使得 channel 和 spatial feature 进行融合，作者说这里是**negligible computational overhead**，即添加这个模块带来的计算增加是可以忽略不计的。

## Someting Related

作者说，通过对通道或者空间信息分别进行 attention 的意义在于，可以使得模型**have the ability to learn where to attend and further focus on the target objects**。这方面做的比较突出的是 SENet（之后我也会去读这篇文章）。

---
### CBAM and SENet

*SENet was succeeded by Convolutional Block Attention Module (CBMA) and Bottleneck Attention Module (BAM), both of which stressed on providing robust representative attentions by incorporating spatial attention along with channel attention.*

*They provided substantial performance gains over their squeeze-and-excite counterpart with a small computational overhead.*


![SENet](/screenshots/SENet.png)

关于SENet，首先利用一个池化层（或者两个池化层：Global Average Pooling and Global Max Pooling）对 spatial information 进行信息聚合，得到形状为 C\*1\*1 的 tensor，之后通过一个 Bottleneck，中间有一个 Relu 层（当然这就是一个 attention 操作）。这里的思路是，将 spatial feature 通过 pooling 操作聚合成 1*1 的形状，之后 relu 操作便是将 channel 的信息进行 attention（负的抛弃掉，正的进行保留），最后通过 expand_as 操作加到原来的 input 上面，即可以理解为将 attention 之后的 channel 信息加到了所有的 spatial feature 上面。

*The channel descriptors are projected into a lower dimensional space and then maps them back.*

---

传统的计算 channel attention（上图左）的方式是*computing channel attention involve computing a singular weight, often a scalar for each channel in the input tensor and then scaling these feature maps uniformly using the singular weight.*

但这样作者说*the input tensor is spatially decomposed to one pixel per channel by performing global average pooling. This result in a major loss of spatial information and thus the inter-dependence between the channel dimension and the spatial dimension is absent when computing attention on these single pixel channels.*
也就是说你虽然对 channel 进行了 attention，但是在这个过程中你缺失了 spatial information。

因此CBAM（上图右）引入了一个Spatial attention用来补充Channel attention的补充，但是这个过程中作者说*channel attention and spatial attention are segregated and computed independent of each other.*

所以作者就想啊，上面的方法channel feature和spatial feature都independent，那我合并一下不就好了？所以作者的Module就是*capture dependencies between the (C, H),(C, W)和(H, W) dimensions of the input tensore respectively*

## Triplet Attention Module

> Investigate how to model cheap but effective channel attention while not invloving any dimensionality reduction.

### Z-pool

这个模块其实就是将 Global Average Pooling 和 Global Max Pooling 进行一个合的聚，这样的好处作者说是 *preserve a rich representation of the actual tensor while simultaneously shrinking its depth to make further computation lightweight.*

聚合方式很简单就是把两个 (1, H, W) 形状的 tensor 用 cat 函数变成 (2, H, W)。

### Triplet Attention

这部分的思路很简单，单拿出来一个举例：

首先将形如 (C, H, W) 进行旋转变成 (W, H, C)，之后输入到 Z-pool 成为形如(2, H, C)，之后输入到k*k卷积核的卷积层+BN中得到 (1, H, C)，最后通过sigmoid激活函数后与原来的值进行相加（这里原文用的是 apply to）并旋转回原来的形状。

这样三个 branches 的结果相加并乘以 1/3 便是最终输出。

![Tri-module](/screenshots/Tri_module.png)

### Result

最后的结果就不贴上来了，总体来说实现了参数量不增加的情况下，准确度有些许提升（原文用的是 error rate）

## Code
xxx