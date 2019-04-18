---
title: spark之RDD sortBy
date: 2019-04-18 14:43:47
tags:
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

## 3.实践
``` scala
    val rdd = sc.parallelize(Seq.range(0, 10), 10).mapPartitionsWithIndex {
      case (index, _) =>
        Array.fill(1 << 30)(index % 2).toIterator
    } // 生成这么多的数据, 元素只有0和1, 每个元素大约1 << 30 * 5个
    println(rdd.count())

    val sortRdd = rdd.sortBy(i => i)
    println(sortRdd.count())
    sortRdd.take(100).foreach(println)
```

DAG
![](2019-04-18-14-48-55.png)
可以看到先构造一个ParalledCollectionRDD

![](2019-04-18-14-55-28.png)
可以看到ShuffleRead和ShuffleWrite

看下BlockStoreShuffleReader的源码
``` scala
    // Sort the output if there is a sort ordering defined.
    dep.keyOrdering match {
      case Some(keyOrd: Ordering[K]) =>
        // Create an ExternalSorter to sort the data. Note that if spark.shuffle.spill is disabled,
        // the ExternalSorter won't spill to disk.
        val sorter =
          new ExternalSorter[K, C, C](context, ordering = Some(keyOrd), serializer = dep.serializer)
        sorter.insertAll(aggregatedIter)
        context.taskMetrics().incMemoryBytesSpilled(sorter.memoryBytesSpilled)
        context.taskMetrics().incDiskBytesSpilled(sorter.diskBytesSpilled)
        context.taskMetrics().incPeakExecutionMemory(sorter.peakMemoryUsedBytes)
        CompletionIterator[Product2[K, C], Iterator[Product2[K, C]]](sorter.iterator, sorter.stop())
      case None =>
        aggregatedIter
    }
```