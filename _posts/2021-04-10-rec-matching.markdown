---
layout:     post
title:      "个性化推荐-召回概述"
subtitle:   "Matching Stage in Recommendation"
date:       2021-04-10
author:     "Jiang Wenrui"
header-img: "img/about-bg.jpg"
tags:
    - 深度学习
    - 个性化推荐
    - 召回
---

## 召回概述

推荐系统一般分为两部分，召回阶段和排序阶段。召回阶段是从全量数据中挑选出用户可能感兴趣的一部分数据，供后面的排序阶段使用.

其典型的流程图如下所示：

<img src="/img/rec-matching/rec_stage_0.png" width="90%" height="90%" />
<small class="img-hint">workflow</small>

各阶段维度如下所示：

<img src="/img/rec-matching/rec_stage.png" width="60%" height="60%" />
<small class="img-hint">workflow detail</small>

> 参考：[Privileged Features Distillation at Taobao Recommendations](https://arxiv.org/abs/1907.05171v2)

召回常见的分类有以下几类：
* 属性召回
* 模型召回
* 其他召回

如下图所示：
<img src="/img/rec-matching/rec_kind.png" width="80%" height="80%" />
<small class="img-hint">召回分类</small>

## 几大模型召回

常见的几大经典模型如下：
* **YoutubeDNN**
* **DSSM**
* **GCN**

首先看看最经典的 YoutubeDNN,其结构如下所示：

<img src="/img/rec-matching/youtubednn_1.png" width="69%" height="69%" />
<small class="img-hint">Youtube DNN结构</small>

> 参考：[Deep Neural Networks for YouTube Recommendations](https://static.googleusercontent.com/media/research.google.com/zh-CN//pubs/archive/45530.pdf)

YoutubeDNN是一篇工程实践很佳的论文，其存在很多需要斟酌的点：

<img src="/img/rec-matching/youtubednn_2.png" width="100%" height="100%" />
<small class="img-hint">Youtube DNN工程实践</small>

然后再看看双塔模型，其结构对比YoutubeDNN有他的优势：
* 用户单塔+softmax vs 双塔
* YoutubeDNN的负采样 由模型控制,位置采样 vs DSSM的是人为控制，灵活性更强，概率采样
* 现在双塔通常融入了一些经典层如 Attention, Capsule

> 参考：[MIND](https://arxiv.org/pdf/1904.08030.pdf)


最后，是近些年兴起的GCN，除了使用side info, 最终的embedding还能融合社交网络的拓扑结构：
<img src="/img/rec-matching/gcn.png" width="80%" height="80%" />
<small class="img-hint">GCN</small>

> 经典的GCN如：[PinSAGE](https://arxiv.org/pdf/1806.01973.pdf)

## 统一召回框架–向量化召回

#### NFEP框架
召回算法，品类众多而形态迥异，看似很难找出共通点。如今比较流行的召回算法，比如：item2vec、DeepWalk、Youtube的召回算法、Airbnb的召回算法、FM召回、DSSM、双塔模型、百度的孪生网络、阿里的EGES、Pinterest的PinSAGE、腾讯的RALM和GraphTR,但其实都可以被一个统一的算法框架所囊括，即NFEP（Near, Far, Embedding, Pairwie-loss）框架，也成为
向量化召回的统一框架

在该框架下，所有召回算法可以从四个维度展开：
1. **Near**: 如何定义X/Y两概念之间的“距离近”？
2. **Far**: 如何举反例，即如何定义X/Y之间的“距离远”？
3. **Embedding**: 如何获取embedding?
4. **Loss**: 如何定义loss来优化？

> 参考：[石塔西](https://www.zhihu.com/people/si-ta-xi)的[万变不离其宗：用统一框架理解向量化召回](https://zhuanlan.zhihu.com/p/345378441), 很推荐博主文章，受益匪浅

首先我们看如何定义近：Near

可以从以下几种召回策略展开：

* **i2i召回**：x,y都是item，我们认为同一个用户在同一个session交互过的两个item在向量空间是相近的，体现两个item之间的“相似性”
* **u2i召回**：x是user，y是item。一个用户与其交互过的item应该是相近的，体现user与item之间的“匹配性”
* **u2u召回**：x,y都是user。比如使用孪生网络，则x是user一半的交互历史，y是同一用户另一半交互历史，二者在向量空间应该是相近的，体现“同一性”

其次我们再看如何定义远：Far

这里涉及到几个问题：
1.负样本如何定义
2.hard negative 如何加

第三是如何定生成向量：Embedding

常见算法策略为：
1.有的算法只使用Id
2.有的算法除此之外，还使用了画像、交互历史等side information
3.有的除了使用side info, 还使用社交网络的拓扑结构，如GCN

最后是如何定义loss来做优化：Loss
主要考虑Pointwise VS Pairwise

套用NFEP框架可以看看常见的DSSM算法：
* **Near**：u2i召回的典型思路
* **Far**：全局负采样，无hard negative
* **Embedding**：user embedding，采用soft-attention捕捉序列交互，feed embedding: share embedding table with user tower
* **loss**：Poinwise-loss: binary cross-entropy

#### 负样本采集
负样本如何采集：
* 排序其目标是“从用户可能喜欢的当中挑选出用户最喜欢的”，是为了优中选优。与召回相比，排序面对的数据环境，简直就是温室里的花朵。
* 召回是“是将用户可能喜欢的，和海量对用户根本不靠谱的，分隔开”，所以召回在线上所面对的数据环境，就是鱼龙混杂、良莠不齐。

所以，要求喂入召回模型的样本，既要让模型见过最匹配的，也要让模型见过最不靠谱的，才能让模型达到"开眼界、见世面"的目的，从而在“大是大非”上不犯错误。

#### hard negative mining
hard negative mining怎么做：

easy negative:属于你能知道一个用户喜欢狗而非猫，但是不知道具体喜欢哪种品种的狗

那么如何选取hard negative？
* 业务逻辑比较明显：比如Airbnb listing embedding, 增加与正样本同城的房间作为hard negative
* 业务逻辑不太明显： 用上一版本的召回模型筛选出"没那么相似"的<user,doc>对，作为额外负样本，训练下一版本召回模型

怎么定义“没那么相似”？论文EBR是拿召回位置在101~500上的物料。排名太靠前那是正样本，不能用；太靠后，与随机无异，也不能用；只能取中段
> 参考：[EBR:Embedding-based Retrieval in Facebook Search](https://arxiv.org/abs/2006.11632v2)

关于hard negative的训练，通常需要采用一种 **渐近式训练策略**， 可以参考[PinSAGE](https://arxiv.org/pdf/1806.01973.pdf)

如果hard negative不容易实现，还可以采用一种思路，就是用不同难度的negative训练不同难度的模型，再做多模型的融合
Serving阶段：fasis检索时，将权重乘在user embedding或item embedding一侧，然后将各个模型产出的embedding拼接起来(𝛼为超参)，这种策略参考[EBR:Embedding-based Retrieval in Facebook Search](https://arxiv.org/abs/2006.11632v2)

#### 召回Loss
再看一下召回中loss的选择：pointwise vs pairwise
<img src="/img/rec-matching/pairwise.png" width="80%" height="80%" />
<small class="img-hint">pointwise vs pairwise</small>

它们的区别如下：
排序：
1. 样本是两条样本对<user,item+,1>与< user,item-,10>

召回：
1. 样本是<user,item+, item->
2. 针对同一个user，与item+的匹配程度，要远高于，与item-的匹配程度。所以Loss中没有label

而常见的pairwise loss有：
* sampled softmax loss
* margin hinge loss/BPR Loss   

#### 召回离线评估
最后是召回离线评估问题，几种常见策略为：
1. 拿Top K召回结果与用户实际点击做交集，然后计算precision/recall
2. 计算“用户实际点击”在“召回结果”中的平均位置

但是这些策略置信度有一定问题：因为召回的结果未被用户点击，未必说明用户不喜欢


## 参考资源


* [石塔西](https://www.zhihu.com/people/si-ta-xi): 石塔西的博文


## 转载声明

首次发布于 [Jiang Wenrui](http://wenruij.github.io)，转载请保留以上链接
