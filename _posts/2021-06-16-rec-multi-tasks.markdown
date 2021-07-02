---
layout:     post
title:      "个性化推荐-综述"
subtitle:   "Multi-Tasks in Deep Recommendation"
date:       2021-06-16
author:     "Jiang Wenrui"
header-img: "img/about-bg.jpg"
tags:
    - 深度学习
    - 个性化推荐
---

## Shared-Bottom Models
优势
这种底层参数共享的方式通常又被称为参数硬共享。这种模型结构由于底层参数被所有目标共享，所以大大【降低了过拟合】的风险，与此同时，不同目标在学习时也可以通过这些共享的参数进行【知识迁移】，利用其它目标学习到的知识帮助自己目标的学习。
缺点
但也正是因为参数的硬共享，限制了各个目标拟合的自由度，影响了拟合的效果。特别是在不同目标之间【相关性比较低】时，会更容易顾此失彼，难以学好每个目标

## MMoE

## Attention-based MMoE
为每个任务随机设置了一个Context向量，作为Attention模块的Query。这个context向量跟不同任务的输入没有关系，纯粹就是随机初始化，然后让模型学，慢慢会学到这个域的信息。

## 任务间依赖关系建模


## 不同Loss权重自动学习
主要有两种策略：
* 不确定性加权
* 帕累托最优

不确定性加权

基于偶然不确定性中的【任务依赖型(同方差)不确定性】，来进行建模，任务依赖型或者同方差不确定性不依赖于输入数据，它可以被描述为任务相关不确定性。
在多任务联合学习中，任务依赖型不确定性能够表示不同任务间的相对难度。
一般假设预测误差满足高斯分布，考虑两个任务的方差分别为sigma_1,sigma_2。可以推导出loss最终形态。

它基于最大化同方差不确定性的高斯似然估计，生产多任务损失函数。

方案：single task loss weighted combined loss， how to manipulate weight

task relational loss weight
Homoscedastic Uncertainty(同方差不确定性)
prediction 的噪声方差作为模型收敛程度(模型预估精准度)的评估，作为比重调整
方差越大，模型收敛和预估越差，最终loss 中该任务的比重需要大一些，模型对其优化的程度需要大一些。

参考：
1. [多任务学习优化](https://zhuanlan.zhihu.com/p/269492239)
2. [Uncertainty Loss不确定损失附代码实现](https://blog.csdn.net/weixin_44385551/article/details/108546618)
3. [深度学习的多个loss如何平衡？](https://www.zhihu.com/question/375794498/answer/1050963528)

帕累托最优
帕累托最优希望模型能够从某个点C开始优化，寻找到帕累托平面上的点（A或者B），使得空间中没有其它situation能够dominate支配现在的situation
假设现在有两个目标函数，解A对应的目标函数值都比解B对应的目标函数值好，则称解A比解B优越，也可以叫做解A强帕累托支配解B

参考：
1. [帕累托最优](https://www.zhihu.com/topic/19619538/hot)
2. [微信看一看用PAPERec框架逼近帕累托最优](https://mp.weixin.qq.com/s/p5ggDo4VkKdcUIkCk0dpIQ)
3. [阿里多目标优化Pareto-Efficient](https://zhuanlan.zhihu.com/p/159459480)

## 参考资源

* [多目标排序模型在腾讯QQ看点推荐中的应用实践](https://mp.weixin.qq.com/s?__biz=MzU2ODA0NTUyOQ==&mid=2247495049&idx=1&sn=83512ea766674b5c79ba14400638436f&chksm=fc91573fcbe6de292ed73dcfe85eac487700652118c17b2d4eec00f69a9f5a6ed4f5d1eb6d1f&mpshare=1&scene=1&srcid=0514pwDYjJ1lgUPCFAtiQRmA&sharer_sharetime=1620970508653&sharer_shareid=138d307faf29e487e5caea4079087f80&version=3.1.8.90238&platform=mac&st=6FEC38292324B947DA7A300BE94F00E6C5109410FC6246E55C902C0F7566DD2B32F95E32C786C85B11DC05BF41477C2A70EF3E98AE8BAC44715DD9795B33A84E63FC6A6D465823B95E6F916B68D07D373961670E61B911A5FC4A4FA87DEA645E76172FDF23C3F72EC7588BF7263D02B73D1117FE313FA4DF6579D2261BB39B8C3C3D6CAD87F38C57F9BF9313D858E664307890316CF0CF0995922A8CB714B371AD28D717B8DA9E913F2EB122D19E59E6&vid=1688852491345368&cst=AD37DC54327E4EDE01F18B42D7EEF0DCA7711F455FBEA621B5F03052E0E94B4A7049109682FE214F0F7E240FCA4CAB65&deviceid=9c19deb4-7af0-48d4-b5a5-1a9bdab56e9b#rd)

## 转载声明

首次发布于 [Jiang Wenrui](http://wenruij.github.io)，转载请保留以上链接