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

### Overview

本文是端到端（End to End）抽取的开山鼻祖，在此之前，联合抽取模型通常都是基于人工构造特征的结构化学习方法，该模型基于 Sequence 信息以及 Dependency Tree 结构信息来抽取实体以及实体关系，常常作为后续实验的Benchmark

- 基于3个Layer（embedding，sequence，dependency）
- Entity：BILOU
- （S，O）预测 P

---
### Feature
- 贪心法decode实体（不用beam search）
- 在Sequence单元中考虑前一个单词的状态
- 生成label embedding

### 树结构
- 最短路径构造节点关系（充分考虑信息）
- Tree-structured LSTM
  - Child-Sum Tree（多类型问题）
  - N-ary Tree（变长度子节点问题）
  - 作者提出的模型（Each type share one Weight）
