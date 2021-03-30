---
layout:     post
title:      "分布式架构基础之PS"
subtitle:   "PS for HugeCTR"
date:       2021-03-30
author:     "Jiang Wenrui"
header-img: "img/post-bg-rwd.jpg"
tags:
    - 深度学习
    - 分布式
---

#### Parameter Server

PS架构最早由google提出。

其典型的架构图如下所示 *（目前与之对立的为All-Reduce）*

<img src="/img/in-post/ps.png" width="50%" height="50%" />
<small class="img-hint">典型的PS架构</small>

> 参考：[Scaling Distributed Machine Learning with the Parameter Server](http://web.eecs.umich.edu/~mosharaf/Readings/Parameter-Server.pdf)

其典型的算法流程如下所示：

<img src="/img/in-post/distributed_algo.png" width="50%" height="50%" />
<small class="img-hint">PS架构算法流程</small>

基于PS架构有以下几种策略：
* **Sequential**：同步并行，所有worker保持同一clock, 效率低，但是收敛效果好。
* **Eventual**：异步并行，所有worker维持自己的clock步伐，效率高，但是收敛效果较差。
* **Bounded Delay**：也叫做Flexible Consistency，即半同步半异步并行。

一个典型的Bounded Delay算法如下所示：

<img src="/img/in-post/eamsgd.png" width="50%" height="50%" />
<small class="img-hint">EAMSGD流程</small>

> 术语：
>
> * *EAMSGD*： Elastic Averaging SGD， from [Deep learning with Elastic Averaging SGD](https://arxiv.org/abs/1412.6651)



#### 一些资源


* [PS-Lite](https://github.com/dmlc/ps-lite): A light and efficient implementation of the parameter server framework
* [SpeeDo](http://ai.deepq.com/speedo/): A deep learning system designed for off-the-shelf hardwares


#### 转载声明

首次发布于 [Jiang Wenrui](http://wenruij.github.io)，转载请保留以上链接
