---
layout:     post
title:      "个性化推荐-综述"
subtitle:   "Personalized Recommendation"
date:       2021-06-06
author:     "Jiang Wenrui"
header-img: "img/about-bg.jpg"
tags:
    - 深度学习
    - 个性化推荐
---

## 综述

个性化推荐所涉及的问题
* **采样**
* **特征**
* **Loss**
* **多兴趣**
* **多任务**
* **冷启动**
* **在线深度学习**
* **评估**


#### 采样

核心词
1. **召回**
2. **粗排**
3. **排序**
4. **样本偏差**
5. **热度打压**
6. **数据扩充**
7. **迁移学习ESAM**
8. **辅助学习ESMM**
9. **空间映射AutoDebias**

#### 特征

核心词
1. **User特征**, **Item特征**, **Author特征**, **Context特征**, **Session特征** 
2. **数值特征**, **类别特征**, **Embedding特征**
> 如：多模态embedding, 点击相似embedding
3. **贝叶斯平滑**, **威尔逊区间平滑**
4. **KV记忆网络**
5. **特征选择**
6. **特征重要度**

#### Loss

核心词
1. **Pointwise**
2. **Pairwise**
3. **Listwise**
4. **极大似然**
5. **交叉熵**
> 本质是对数损失函数
6. **Margin Hinge/BPR**
> BPR也是是对数损失函数
7. **Sampled Softmax Loss**
> 本质也是对数损失函数： argmin{-sum(log(softmax(user_emb*item_emb)))} for u2i models
> 如：YoutubeDNN Loss，Airbnb Listing Embedding中的Skip-gram Model Loss
8. **正则**

#### 多兴趣

核心词
1. **MIND**
2. **MIT**
3. **Transformer**

#### 多任务

核心词
1. **MMoE**
2. **Attention based MMoE**
3. **依赖关系建模ESMM**

#### 冷启动

核心词
1. **Bandit策略for用户冷启动**
2. **User GCN Embedding**
3. **多模态Embedding**
4. **点击相似Type Embedding/Skip-gram Model**
5. **贝叶斯平滑点击率**

#### 在线深度学习

核心词
1. **流式样本生成**
2. **特征独立同分布**
3. **词表OOV**
4. **批流结合训练**
5. **延迟反馈建模**

#### 评估

核心词
1. **precision/recall**
2. **平均位置**
3. **auc/gauc**
4. **时效性**
> example age, 增量训练, 缩短待预测内容池
5. **多样性**


## 转载声明

首次发布于 [Jiang Wenrui](http://wenruij.github.io)，转载请保留以上链接