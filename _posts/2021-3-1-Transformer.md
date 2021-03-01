---
title: 'Transformer Interpretability Beyond Attention Visualization'
tags: paper CV Y2021
---
今天分享的论文是新发表在 CVPR2021 上的，作者用 transformer 代替了传统的卷积网络 CNN。因为 transformer在 NLP 领域有着相当高的地位，感觉之后对于 NLP 和 CV 的结合操作有着很好的前途。

## Abstract
自注意力机制（尤其是 transformers）在文字处理领域有着主导地位，并且在 CV 领域也变得越来越流行。目前的方法为了得到图像中可以导致正确分类的部分，要么是使用注意力映射或者在注意力图上采用启发式传播 *heuristic propagation along the attention maps*

作者提出了一种对 Transformer 网络计算相关性的方法，该方法可以基于泰勒展开*Taylor decomposition proinciple*分配局部关联，之后将这种局部关联沿着整个网络进行传播，包括注意力层和跳跃链接 [^1]。[^1]:skip connection

该方法可以保持各层之间的总体相关性。


## Introduction
