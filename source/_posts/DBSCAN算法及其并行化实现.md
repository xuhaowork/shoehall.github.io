---
title: DBSCAN算法及其并行化实现
date: 2019-05-29 09:29:40
tags:
---

## DBSCAN算法及其并行化实现

###1.目录
1)目录
2)DBSCAN算法
3)DBSCAN算法的特性
4)DBSCAN算法延伸
一般DBSCAN的实现
kd树, BBD树, r树
5)DBSCAN并行化(MR框架)
6)一款综合性的DBSCAN算法

###2.DBSCAN算法
DBSCAN算法是最著名的基于密度的聚类算法


###3.DBSCAN算法的特性
1)对输入数据的顺序不敏感
2)可以识别任意形状的数据
3)可以识别离群点
缺点:
1)参数敏感
2)不同密度的类簇

###4.DBSCAN算法延伸



###5.DBSCAN算法并行化
目前已经实现的:
https://github.com/alitouka/spark_dbscan
MRDBSCAN-FCS-Feb.2014.pdf


