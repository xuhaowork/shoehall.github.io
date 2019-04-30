---
title: Spark mapPartition中Iterator的使用技巧
date: 2019-04-02 17:18:41
categories: 
  - 算法和程序实现
tags:
  - Spark
---

# spark mapPartition中Iterator的使用技巧

## 1.mapPartition中Iterator的迭代简介
利用Iterator直接定义一个新的Iterator
1)不用转存为数组等结构, 防止数据过大栈溢出
2)限制是不适合前后调用跨度特别大的mapPartition函数

## 2.示例
```
    import cn.datashoe.sparkBase.TestSparkAPP
    val a = TestSparkAPP.sc.parallelize(1 to 20, 2)

    def diff1(iter: Iterator[Int]): Iterator[Int] = {
      if (iter.isEmpty)
        Iterator[Int]()
      else {
        var value = iter.next()
        new Iterator[Int] {
          def hasNext: Boolean = iter.hasNext

          def next(): Int = {
            val lastOne = value
            value = iter.next()
            value - lastOne
          }
        }
      }

    }

    /**
      * 结果解析
      * ----
      * 第1个分区
      * (1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
      * 结果
      * (1, 1, 1, 1, 1, 1, 1, 1, 1)
      *
      * 第2个分区
      * (11, 12, 13, 14, 15, 16, 17, 18, 19, 20)
      * 结果
      * (1, 1, 1, 1, 1, 1, 1, 1, 1)
      */
    println("原数据")
    a.mapPartitionsWithIndex {
      case (partitionId, iter) =>
        Iterator((partitionId, iter.mkString(", ")))
    }.foreach(println)

    println("差分后数据")
    a.mapPartitions(diff1).mapPartitionsWithIndex {
      case (partitionId, iter) =>
        Iterator((partitionId, iter.mkString(", ")))
    }.foreach(println)
```

## 3.解析
### 1)上述案例没有将数据存入Array或Buffer中, 避免了一个分区数据过大时栈溢出
下述代码的问题是toArray时会将所有数据放入一个Array中, 可能放不下, 另外也多一次操作
```
    def diff2(iter: Iterator[Int]): Iterator[Int] = {
      val arr = iter.toArray
      arr.sliding(2).map {
        s =>
          s(1) - s(0)
      }
    }
```
### 2)上述案例对于一些操作并不是很合适, 比如涉及到前后操作的情况

### 3)场景：一个Iterator进行两次操作
由于Iterator没法重置因此同一个Iterator是一次性使用完毕的。没法再同一段程序中重复遍历两次。