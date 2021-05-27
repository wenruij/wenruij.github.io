---
layout:     post
title:      "个性化推荐-Attention概述"
subtitle:   "Attention in Recommendation"
date:       2021-05-10
author:     "Jiang Wenrui"
header-img: "img/about-bg.jpg"
tags:
    - 深度学习
    - 个性化推荐
---

## Attention概述

如何简洁的定义Attention:
> Attention机制的作用就是对信息进行更好地加权融合

他的核心逻辑就是「从关注全部到关注重点」。

其计算流程图如下所示：

<img src="/img/attention/attention.png" width="90%" height="90%" />
<small class="img-hint">Attention计算</small>

详细过程如下：

1. query 和 key 进行相似度计算，得到权值
2. 将权值进行归一化，得到直接可用的权重
3. 将权重和 value 进行加权求和

RNN 时代是死记硬背的时期，attention 的模型学会了提纲挈领，进化到 transformer，融汇贯通，具备优秀的表达学习能力

## Attention分类

常见的几大经典Attention：
* **Self-Attention**
* **Multi-Head Attention**
* **Label-Aware Attention**
* **Soft Attention**

其中Self-Attention与Multi-Head Attention，不同于其他普通的attenion,其他普通attention输出的是一个单个的向量，而Self-Attention与
Multi-Head Attention输出的是与Value个数一样的多个向量

Transformer的核心构造就是Multi-Head Attention

另外，[Multi-Head Attention with Disagreement Regularization](https://arxiv.org/abs/1810.10183)一文中，希望通过对不同的Head加正则的方式，来提升不同Head学习的差异性


## 参考资源

* [Rethink深度学习中的Attention机制](https://zhuanlan.zhihu.com/p/125145283)
* [More About Attention](https://zhuanlan.zhihu.com/p/106662375)
* [The Illustrated Transformer](https://jalammar.github.io/illustrated-transformer/)
* [一文看懂 Attention:本质原理+3大优点+5大类型](https://zhuanlan.zhihu.com/p/91839581)

## 转载声明

首次发布于 [Jiang Wenrui](http://wenruij.github.io)，转载请保留以上链接
