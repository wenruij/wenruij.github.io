---
layout:     post
title:      "个性化推荐-粗排概述"
subtitle:   "Coarse Matching in Recommendation"
date:       2021-04-12
author:     "Jiang Wenrui"
header-img: "img/about-bg.jpg"
tags:
    - 深度学习
    - 个性化推荐
    - 粗排
---

## 粗排概述

粗排通常是介于召回与排序之间的阶段，如下图所示：

<img src="/img/rec-matching/rec_stage.png" width="60%" height="60%" />
<small class="img-hint">workflow detail</small>

> 参考：[Privileged Features Distillation at Taobao Recommendations](https://arxiv.org/abs/1907.05171v2)

我们来看一下召回，排序，粗排 这三个阶段：

召回：是一种集合选择方案
* **目标**：以集合(topK)为建模目标
* **优点**：算力消耗小
* **缺点**：表达能力有限，目标对齐程度低
* **方法**：lambdaMART/集合评估器/实时播放率

排序：是一种排序预估方案
* **目标**：以预估值(prediction)为建模目标
* **优点**：表达能力强，目标对齐程度高
* **缺点**：算力消耗大
* **方法**：双塔架构/底层工程优化

粗排：
* 防止召回到精排的漏斗过大
* 相较于召回，更强调排序(目标约束)
* 相较于精排，更强调性能(算力约束)

## 粗排之知识蒸馏

首先知识蒸馏既可以用于粗排，也可用于精排。
粗排阶段的知识蒸馏，主要用于解决:
1. 粗排双塔模型结构的不足，此时可以采用模型蒸馏
2. 补足特征上的缺陷，即补足交叉特征的收益，补足条件特征的收益

而精排阶段的知识蒸馏，通常用于解决：
1. 补足特征上的缺陷：补足条件特征的收益

比如在精排阶段，用户在商品页的行为对CVR的预估非常有用，但是线上服务时是无法获取这类后验特征的。于是在精排CVR预估中，这类特征就是优势特征。

#### 优势特征 Privileged Features

那啥叫Privileged Features？作者说，你们之前模型用的那些在线特征啊，Naive，比如你知道用户停留时长这个指标对于CVR的预测有多重要吗？你可能会说了，线上服务时我们没法拿到停留时长这样的后验特征啊，那为了保持线上线下特征的一致性，只能忍痛割爱。你说对了，这种信号强，但又不听话只能离线获取的特征，即条件特征，就叫优势特征

还有一种粗排模型处理不了的交叉特征，也是优势特征

故而常见优势特征有：
* 条件特征/后验特征
* 交叉特征

#### 特征蒸馏

特征蒸馏通常模型相同，但是特征不同，其目的就在于：
1. 用于补足交叉特征的收益
2. 用于补足条件特征的收益

其详述过程是这样的：
1. 在离线环境下，会同时训练两个模型：一个学生模型和一个教师模型。
2. 其中教师模型额外利用了优势特征，则准确率更高。
3. 将教师模型蒸馏出来的知识传递给学生模型，辅助其训练，提升学生的准确率。
4. 线上服务时，只用学生模型进行部署，由于输入中不依赖优势特征，则保证了线上线下特征的一致性。
5. 本文中的蒸馏知识是通过教师模型中的最后一层输出进行传递。这个模型可以起名为PFD = Privileged Features Distillation

#### 模型蒸馏
而模型蒸馏，表示模型不同，但是特征相同，其目的就在于：
1. 用于补足结构交互的收益
2. 本质上是模型压缩

为什么说补足交互结构的收益呢？因为当下粗排阶段的模型以**双塔模型**为主，而复杂的交叉特征和交叉结构通常不会用于双塔模型
双塔模型通常：
* user/item 结构解耦
* 内积计算的算力消耗小

另外，粗排阶段的模型方案演化如下图所示：
<img src="/img/pre_ranking/pre_ranking.png" width="90%" height="90%" />
<small class="img-hint">粗排模型演化</small>

> 参考：[COLD](https://arxiv.org/pdf/2007.16122.pdf)

#### 统一蒸馏

统一蒸馏，也就是模型蒸馏 + 特征蒸馏，其示意图如下：
<img src="/img/pre_ranking/distillation.png" width="60%" height="60%" />
<small class="img-hint">统一蒸馏</small>
其中，Soft Labels 是由 Teacher Model 学习并输出的

这里开始，介绍蒸馏学习的两种训练方案：
1. 分离训练
2. 联合训练

介绍两种训练之前，先看蒸馏训练时涉及到三种loss:
* Student Loss = Ground Truth Loss
* Teacher Loss
* Distillation Loss

分离训练：
1. 优先训练好Teacher Model
2. 用训练好的Teacher Model指导Student Model的训练
Student Model的Loss如下：
<img src="/img/pre_ranking/loss1.png" width="80%" height="80%" />

联合训练：
1. 同步更新Teacher Model和Student Model中的参数

联合模型的Loss如下：
<img src="/img/pre_ranking/loss2.png" width="80%" height="80%" />

联合训练的一个风险：
这种方法可能带来训练的不稳定，尤其是训练初期，Teacher Model自己还没有学好，就来指导Student Model，是有可能导致训练偏离正常的。因此λ参数就很重要，初期可以设为0

#### 常见粗排蒸馏方案
常见的粗排蒸馏方案是： 精排到粗排的蒸馏
比如：
<img src="/img/pre_ranking/train.png" width="75%" height="75%" />

有如下特性：
1. 补足双塔模型在结构和特征上的缺陷
2. 目标对齐
3. 采用分离训练
4. 让粗排反向学习精排的打分结果

## 其他粗排方案
其他粗排方案如：级联模型，COLD

#### 级联模型
具体可参考：[爱奇艺短视频推荐：粗排篇](https://mp.weixin.qq.com/s?__biz=MzU1NTMyOTI4Mw==&mid=2247533568&idx=1&sn=2b8c694ff43bb451ff1e70b49a7bd97b&chksm=fbd7f66ccca07f7a29d0eb0389cd4231707d6588219af0fff94b22e43d8106161172aff9f16c&mpshare=1&scene=1&srcid=0227rNIffc1uT7Z3QXoL2TrR&sharer_sharetime=1615852936703&sharer_shareid=89f69aa470e51c1cf885957926ef3e00&version=3.1.6.90174&platform=mac#rd)

#### 下一代粗排COLD
具体可参考：[阿里定向广告最新突破：面向下一代的粗排排序系统COLD](https://zhuanlan.zhihu.com/p/186320100)

## 参考资源

* [深入浅出优势特征蒸馏在淘宝推荐中的应用](https://zhuanlan.zhihu.com/p/155935427): KDD 2020
* [阿里粗排技术体系与最新进展](https://mp.weixin.qq.com/s?__biz=MzU1NTMyOTI4Mw==&mid=2247530926&idx=1&sn=43527db30cd5d0584a8f112426d8720f&chksm=fbd7cbc2cca042d4c0620396c3acc6752c8b6a9f5c7980a9f7ac44a72c040e0d0763ced31606&mpshare=1&scene=1&srcid=0221yHwr0DiJPtOy1lRitBoX&sharer_sharetime=1615852953502&sharer_shareid=89f69aa470e51c1cf885957926ef3e00&version=3.1.6.90174&platform=mac#rd)
* [爱奇艺短视频推荐：粗排篇](https://mp.weixin.qq.com/s?__biz=MzU1NTMyOTI4Mw==&mid=2247533568&idx=1&sn=2b8c694ff43bb451ff1e70b49a7bd97b&chksm=fbd7f66ccca07f7a29d0eb0389cd4231707d6588219af0fff94b22e43d8106161172aff9f16c&mpshare=1&scene=1&srcid=0227rNIffc1uT7Z3QXoL2TrR&sharer_sharetime=1615852936703&sharer_shareid=89f69aa470e51c1cf885957926ef3e00&version=3.1.6.90174&platform=mac#rd)
* [阿里定向广告最新突破：面向下一代的粗排排序系统COLD](https://zhuanlan.zhihu.com/p/186320100)
* [Privileged Features Distillation at Taobao Recommendations](https://arxiv.org/abs/1907.05171v2)
* [COLD](https://arxiv.org/pdf/2007.16122.pdf)


## 转载声明

首次发布于 [Jiang Wenrui](http://wenruij.github.io)，转载请保留以上链接