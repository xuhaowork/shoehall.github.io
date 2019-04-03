---
title: Spark RDD排序
date: 2019-04-02 17:18:41
categories: 
  - 计算框架
tags:
  - Spark
---

# spark RDD排序

## 1.RDD排序原理
1)先通过蓄水池抽样大致了解数据的分布
2)设定分区边界, 将数据分区为{p1, p2, p3, ...}，确保p1的数据小于p2，p2的数据小于p3 
3)对每个分区内调用排序计算

目前存在的疑问: 
1)数据倾斜怎么办?一个分区过大，仍会分在该分区内?
2)分区内的排序算法是否利用数组等数据结构进行排序，当一个分区内数据超过1<<32时是否会发生栈溢出
3)分区内的排序算法还没有看到源码

## 2.部分源码
```
  def sortByKey(ascending: Boolean = true, numPartitions: Int = self.partitions.length)
      : RDD[(K, V)] = self.withScope
  {
    val part = new RangePartitioner(numPartitions, self, ascending)
    new ShuffledRDD[K, V, V](self, part)
      .setKeyOrdering(if (ascending) ordering else ordering.reverse)
  }
```
至于其中ShuffledRDD怎样实现其中的ordering还未看到.

