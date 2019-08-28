---
title: 结绳记事-Spark GraphX学习笔记
date: 2019-06-23 18:34:58
tags:
-Spark
-GraphX
-图
---

## Spark GraphX学习笔记

通过Spark GraphX进行图学习, 发现自己在图以及图应用领域知识太匮乏了, 忍不住做一下笔记以便后面可以发散整理.

### 知识图谱
- kitchen sink图尝试编码人类所有的知识图谱
- Cyc知识库 OpenCYC
- YAGO多语言知识库（包含中文）
OpenKG收集和整理国内国外重要的开放知识库和知识图谱项目，并组织整理相关的中文资料免费对外开放。

YAGO是由德国马普研究所研制的链接数据库。YAGO主要集成了Wikipedia、WordNet和GeoNames三个来源的数据。YAGO将WordNet的词汇定义与Wikipedia的分类体系进行了融合集成，使得YAGO具有更加丰富的实体分类体系。YAGO还考虑了时间和空间知识，为很多知识条目增加了时间和空间维度的属性描述。目前，YAGO包含1.2亿条三元组知识。YAGO是IBM Watson的后端知识库之一

### 为什么要图数据库或者图计算
普通关系型数据库的不足
1）朋友圈发一条消息给所有朋友的朋友
2）采用Kevin Bacon六度模式，Google任何人和Kevin Bacon的联系

### 可以通过图结构表示的数据
1）networks
2）tree（树其实是无环图）
3）RDBMS
4）邻接稀疏矩阵
5）kitchen sink

### 自然图的一些规律
1）图的度遵循幂律


### 图数据库和图处理系统
1）图数据库：提供数据库事务、查询语言、简单的增量更新和持久化
2）图处理系统（GraphX）：分布式内存处理、快速、长时间的独立运算。而基于Pregel的图处理系统在查询顶点和边表现很差，但在执行 PageRank 这类大规模并行算法方面表现很好
3）图数据库：Neo4j、Titan、Oracle Spatial、Graph

### 关于GraphX
1）Apache Giraph，基于MR
2）GraphLab、GraphX和Apache Giraph各自独立实现了 Google Pregel
3）图算法：PageRank、推荐系统、最短路径、社区发现等

### 图理论的基础知识
1）有向图和无向图
2）有环图和无环图
3）平行边
4）自环
5）GraphX是伪图，可能存在平行边或自环，这两种情况需要通过groupEdges和subgraph操作来排除
6）二分图，源顶点和目标顶点来自不同的集合，两个集合内部不存在相连的边
7）RDF图和属性图
8）图查询系统：SPARQL（是W3C推出的图查询语句，和SPARK没关系不要误解了）/Cypher/Tinkerpop Gremlin/GraphX 

