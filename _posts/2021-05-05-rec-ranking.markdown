---
layout:     post
title:      "个性化推荐-排序概述"
subtitle:   "CTR in Recommendation"
date:       2021-05-05
author:     "Jiang Wenrui"
header-img: "img/about-bg.jpg"
tags:
    - 深度学习
    - 个性化推荐
    - 排序
---

## 排序概述

推荐系统一般分为两部分，召回阶段和排序阶段。而排序常用的点击率预估（a.k.a. CTR模型）在搜索、推荐和广告等互联网应用中扮演了至关重要的角色.
本文对排序的描述主要集中于CTR模型的阐述

## CTR结构划分

CTR模型的整体结构可以分为3层：
1. **Embedding Layers** :这一层的作用是将类别型特征(包括数值型特征一般离散化为类别型特征)对应的高维空间映射到Embedding向量的低维空间；
2. **Hidden Layers** :这一层的作用是提供高度非线性的拟合能力；
3. **Output Layers** :这一层的作用是对任务的具体目标进行针对性表达。

不同层的优化路径有显著差异，整体分类框图可以参考：
<img src="/img/rec-ranking/ctr01.png" width="90%" height="90%" />

常见的优化比如：
1. **Hidden Layers特点** :传统全连接层MLP虽然有万能的拟合能力，但研究表明它的业务针对性较弱，通常需要有显式的结构设计才能让模型的学习更加聚焦。
2. **Embedding Layers特点** :大规模数据场景下建模的重中之重，该层参数规模几乎决定了整体存储规模，Embedding表征学习能力决定了模型预估能力的基本盘。


## CTR几大优化方向
如果从Hidden Layers展开，CTR可以从以下几个方面展开优化：
* Interaction Learning
* Sequence Learning
* Graph Learning

Sequence Learning，也就是序建模，是User Behavior Modeling的一种常见策略，常见的方法有：
1. Sum/Mean Pooling
2. RN
3. Attention

Graph Learning，也就是应用于推荐系统的GNN模型，即图建模，对比序列建模，有如下优势：
序建模
* 聚焦在用户自身的历史行为（私域行为）,行为越丰富效果越好
* 能够充分挖掘其个性和差异化特点

图建模
* 利用群体行为互联的空间拓扑结构信息（公域行为），对于低活用户和搜索新需求非常友好
* 借助群体智慧充分挖掘行为背后的共性和可迁移性特点

接下来重点关注Interaction Learning

## CTR Interaction Learning

Interaction Learning的主要手段是 FM 结构，本篇要介绍的FM主要包含以下几大模型
<img src="/img/rec-ranking/fm.png" width="100%" height="100%" />

最终实现效果突出的主要参考：[NFM](https://arxiv.org/abs/1708.05027)和[FwFM](https://arxiv.org/abs/1806.03514v2)，
相比原论文，实现时主要有两点差异：
1. tf.sparse_tensor_dense_matmul替代tf.nn.embedding_lookup
2. 加入域的概念

#### xNFM
比如xNFM vs NFM，首先看NFM:
1. 没有域的概念，所有特征需要先转成category类型，然后为所有特征分配全局唯一的global_id，然后去做embedding lookup查表
2. 采用embedding_lookup，生成embedding后，维度是三维：[batch_size, num_feats, embedding_size]
3. 调用bi_interaction公式

而xNFM
1. 引入域的概念，每一个域对应一个SparseTensor
2. 每一个SparseTensor(域)经由tf.sparse_tensor_dense_matmul处理后得到一个DenseTensor, 然后将多个域的DenseTensor Concat成一个DenseTensor, 生成embedding后，维度是二维：[batch_size, total_embedding_size]
3. 对于这一个DenseTensor，调用FM的interaction公式

#### NFwFM
NFwFM vs FwFM类似上述做法，首先是FwFM：
1. 没有域的概念
2. 基于FM
3. FM基础上，Feat_i 与 Feat_j做 交叉时多一个权重 r_ij

而NFwFM
1. 引入域的概念，每一个域对应一个SparseTensor
2. 每一个SparseTensor(域)经由tf.sparse_tensor_dense_matmul处理后得到一个DenseTensor，生成embedding后，维度是三维：[batch_size, num_fields, embedding_size]
3. 遍历域对每两个域做交叉,只是Field_i 与 Field_j做 交叉时多一个权重 r_ij

## 参考资源

* [你真的懂点击率CTR建模吗?](https://mp.weixin.qq.com/s?__biz=Mzg3MDYxODE2Ng==&mid=2247484017&idx=2&sn=0fdd3f49b1c5c101486a2318986473d5&chksm=ce8a4728f9fdce3e1803ef08da66f18892bd86c8f1a9258c49a4f4fd97ac14ed6008c15c6304&mpshare=1&scene=1&srcid=0601QItKIILJv1QdLk4NfebQ&sharer_sharetime=1622526587047&sharer_shareid=89f69aa470e51c1cf885957926ef3e00&version=3.1.6.90174&platform=mac#rd)
* [多目标排序模型在腾讯QQ看点推荐中的应用实践](https://mp.weixin.qq.com/s?__biz=MzU2ODA0NTUyOQ==&mid=2247495049&idx=1&sn=83512ea766674b5c79ba14400638436f&chksm=fc91573fcbe6de292ed73dcfe85eac487700652118c17b2d4eec00f69a9f5a6ed4f5d1eb6d1f&mpshare=1&scene=1&srcid=0514pwDYjJ1lgUPCFAtiQRmA&sharer_sharetime=1620970508653&sharer_shareid=138d307faf29e487e5caea4079087f80&version=3.1.6.90174&platform=mac&st=27485F13013EB3AC213F91FFAC9C52B1AD88CE5ADEF32015717D1D122C76522D11B103431656276154964BFF074E37ADF2AC7D5CA806B4FE535A722CAF34A409B53B7B1F164E1E5D40D79C2E32DBB175E3F6CC15F86617A90ADE27B05BCC830DD8531091419B8E134D3A85ACADFCAB208B161608654CA2B6040D4DB1EC4B226E579F46647ACC5EB6EA7AD6C388D75C69A01B56C27E848D9F671BF3B5CF8410F6722A698AFEC33154A04CB9C5C56D49A3&vid=1688852491345368&cst=3809AD51FA954428AA30148AC2B98F737BB4CAC55BE6441D1A874866C1EB9E3E22D20E229B37730C864289DC0FEADB2F&deviceid=9c19deb4-7af0-48d4-b5a5-1a9bdab56e9b#rd)
* [一口气放出三篇SIGIR论文！详解阿里妈妈搜索广告CTR模型演进](https://mp.weixin.qq.com/s/0z0JIGU6t4VnMqS_1qDTWg)
* [FM](https://www.csie.ntu.edu.tw/~b97053/paper/Rendle2010FM.pdf)
* [NFM](https://arxiv.org/abs/1708.05027)
* [FwFM](https://arxiv.org/abs/1806.03514v2)
* [DCN](https://arxiv.org/abs/1708.05123v1)
* [xDeepFM](https://arxiv.org/abs/1803.05170v3)

## 转载声明

首次发布于 [Jiang Wenrui](http://wenruij.github.io)，转载请保留以上链接