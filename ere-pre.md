# ERE: ENTITY RELATION EXTRACTION | 实体关系抽取
## 信息管理系 金笑缘
## 2021年11月17日
---


# 关于ERE
- 在NLP中的地位
- 主流的工作
  - Pipeline 流水线方法（先抽取实体，再抽取关系）
  - Joint 联合抽取方法（共享参数，端到端）

---

## 实体抽取
- NER 命名实体识别
- 序列标注方法
  - 序列标注标签设计（BIO、BIESO）

---

## 关系抽取
- SPO三元组（Subject，Predicate，Object）
- 预测顺序非常重要（特别注意P一般是有限）
- 遍历（S，P），预测O（S在具体的文档里是有限的）
- 遍历（S，O），预测P

---

## 关系抽取有一些特别的问题
- 端到端问题（END-TO-END）
- OCR问题
- 图片 -> 文字
- 图片 -> 切割图片 -> 文字

## 联合抽取的特点
- 模型更加简洁（但是模型的设计思路更加复杂）
- 共享参数能更好的学习

---
## 关系抽取有一些特别的问题
- 实体关系重叠问题
  - 同一对（S，O），有多个P
  - 拜登是美国人，拜登是北国总统
- 实体重叠问题
  - 同一个S有多对（P，O）
  - 北京是中国的首都，北京位于北方
- 如何实现参数的共享
  - word embedding
  - layer embedding
  - external embedding


---
# 一些模型

---
# 一些模型

- 来源
- 排序

---
## Model1：End-to-End Relation Extraction using LSTMs on Sequences and Tree Structures【843】
https://aclanthology.org/P16-1105/

### Overview

本文是端到端（End to End）抽取的开山鼻祖，在此之前，联合抽取模型通常都是基于人工构造特征的结构化学习方法，该模型基于 Sequence 信息以及 Dependency Tree 结构信息来抽取实体以及实体关系，常常作为后续实验的Benchmark

- 基于3个Layer（embedding，sequence，dependency）
- 实体：BILOU
- 关系：树结构下（S，O）预测 P

---
### Feature
- 贪心法decode实体（不用beam search）
- 在Sequence单元中考虑前一个单词的状态
- 生成label embedding

---
### 树结构
- 普通的LSTM是基于上一个节点C(t) = t - 1
- 最短路径构造节点关系（充分考虑信息）
- Tree-structured LSTM
  - Child-Sum Tree（多类型问题）
  - N-ary Tree（变长度子节点问题）
  - 作者提出的模型（Each type share one Weight）

---
### 基于 Dependency 的关系抽取
- 选择两个节点
- 根据最短路径确定三个节点
- 选择三个隐层向量进行拼接（有方向）
- softmax

---
### 总结
- 实现了端到端
- 分别基于两个Bi-LSTM
- 依赖较多额外的外部信息（POS，Dependency）
- LSTM的公式比较多

---
## Model2: Joint Extraction of Entities and Relations Based on a Novel Tagging Scheme【313】

ACL2017 https://arxiv.org/abs/1706.05075

---
### 模型结构
- Bi-LSTM 编码器
- LSTM 解码器
- 2 * 4 * |R| * 1 标签系统

---
### 标签系统
- 2 * 4 * |R| + 1
- 2: start and end (Subject or Object)
- 4: BIES
- R: num of relation types
- 1: O

---
### 偏置损失函数
- 看起来特别复杂
- 开关函数
- 降低O标签对损失的影响

---
### 总结
- Encode ~ Decode
- 新的序列标注体系（存在解码局限）
- 标注偏置函数（自定义的LSTM）
- 纵向对比
  - 更好的参数共享
  - 不能解决关系重叠问题


---
## Model3: Joint entity recognition and relation extraction as a multi-head selection problem【145】

2018 https://arxiv.org/abs/1804.07847
期刊文章，非常细致

---
### 模型结构
- Bi-LSTM Encoder
- CRF rule of label
- Sigmoid Layer

### 总结
- 使用 Semantic Head 表示语义（类似CLS）
- multi-head 能够解决关系重叠问题
- 不能解决实体重叠问题（每个word只能出现在一个phrase中）

---
## Model4: GraphRel: Modeling Text as Relational Graphs for Joint Entity and Relation Extraction【147】

ACL2019 https://www.aclweb.org/anthology/P19-1136.pdf
引入了图结构（interactions between entities and relations）

---
### 模型结构
- 阶段一
  - Word Embedding, POS Embedding
  - 依存句法树产生图结构
  - Bi-GCN（无向图 * 2）局部依赖特征
- 阶段二
  - Weighted Bi-GCN（分两个步骤的主要原因）
- 四个Loss
- 图结构如何解决 phrase 的问题

---
### GCN
- 局部依赖信息，向邻接节点传播信息