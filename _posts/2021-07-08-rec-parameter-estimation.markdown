---
layout:     post
title:      "个性化推荐-参数估计"
subtitle:   "Parameter Estimation in Deep Recommendation"
date:       2021-07-08
author:     "Jiang Wenrui"
header-img: "img/about-bg.jpg"
tags:
    - 深度学习
    - 个性化推荐
---

## 常见方法

1. 极大似然估计MLE
2. 最大后验估计MAP
3. 贝叶斯估计

#### 极大似然估计MLE

#### 最大后验估计MAP

#### 贝叶斯估计

#### 共轭分布与共轭先验

#### 熟练掌握

常见分布的似然函数本质就是常见分布的概率密度函数

1. 伯努利分布的对数似然函数
2. 二项分布的对数似然函数
3. 高斯分布的对数似然函数
4. 拉普拉斯分布的对数似然函数
5. Softmax的对数似然函数

#### 常见示例

* 点击事件服从伯努利分布， 依据极大似然估计可以推导出交叉熵
* 线性回归，误差满足高斯分布，依据极大似然估计可以推导出最小二乘法
* 多任务不确定性加权，噪声满足高斯分布，依据极大似然估计可以推导出 加权loss公式
* l1正则化可通过假设权重w的先验分布为拉普拉斯分布，由最大后验概率估计导出
* l2正则化可通过假设权重w的先验分布为高斯分布，由最大后验概率估计导出

## 参考资源

* [深入浅出极大似然估计和极大后验概率估计](https://cloud.tencent.com/developer/article/1693270)
* [贝叶斯估计、最大似然估计、最大后验概率估计](http://noahsnail.com/2018/05/17/2018-05-17-%E8%B4%9D%E5%8F%B6%E6%96%AF%E4%BC%B0%E8%AE%A1%E3%80%81%E6%9C%80%E5%A4%A7%E4%BC%BC%E7%84%B6%E4%BC%B0%E8%AE%A1%E3%80%81%E6%9C%80%E5%A4%A7%E5%90%8E%E9%AA%8C%E6%A6%82%E7%8E%87%E4%BC%B0%E8%AE%A1/)
* [极大似然估计与贝叶斯估计常见符号说明](https://blog.csdn.net/liu1194397014/article/details/52766760)
* [极大似然估计和贝叶斯估计](https://zhuanlan.zhihu.com/p/61593112)
* [回归分析的前提假设](https://www.jianshu.com/p/7a839080bb8b)
* [线性回归详解](https://zhuanlan.zhihu.com/p/53979679)

## 转载声明

首次发布于 [Jiang Wenrui](http://wenruij.github.io)，转载请保留以上链接