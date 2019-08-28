---
title: 拾遗-spark窗口函数和分区
date: 2019-06-17 17:19:07
tags:
---

## 拾遗-spark窗口函数和分区

### spark SQL窗口函数
窗口函数中要注意向spark_partition_id等和分区密切相关的函数, *如果和窗口函数在同一个SQL时spark_partition_id获得的是分区前的id*

### spark SQL窗口函数和分区排序
```scala
val data = spark.sqlContext.sparkContext.parallelize(
      Seq(
        ("a", 0),
        ("a", 1),
        ("a", 2),
        ("a", 3),
        ("a", 4),
        ("b", 0),
        ("b", 1),
        ("b", 2),
        ("b", 3),
        ("b", 4)
      ), 5
    )

    val rdd = data.map{ case (k, v) => Row(k, v)}

    rdd.cache()

    val df = spark.createDataFrame(rdd,
      StructType(
        Array(
          StructField("key", StringType),
          StructField("value", IntegerType)
        ))).withColumn("partition0", spark_partition_id)

    val uu = df
    uu.show()
```
```bash
+---+-----+----------+
|key|value|partition0|
+---+-----+----------+
|  a|    0|         0|
|  a|    1|         0|
|  a|    2|         1|
|  a|    3|         1|
|  a|    4|         2|
|  b|    0|         2|
|  b|    1|         3|
|  b|    2|         3|
|  b|    3|         4|
|  b|    4|         4|
+---+-----+----------+
```

```scala
val windowSpec = Window.partitionBy("key").orderBy(col("value").desc)
val result = df.select(col("key"),
    col("value"),
    rank().over(windowSpec).as("rank"),
    dense_rank().over(windowSpec).as("dense_rank"),
    row_number().over(windowSpec).as("row_number"),
    col("partition0"),
    spark_partition_id.as("partition1")
).withColumn("partition2", spark_partition_id)
result.show()

```

```bash
+---+-----+----+----------+----------+----------+----------+----------+
|key|value|rank|dense_rank|row_number|partition0|partition1|partition2|
+---+-----+----+----------+----------+----------+----------+----------+
|  b|    4|   1|         1|         1|         4|         4|       161|
|  b|    3|   2|         2|         2|         4|         4|       161|
|  b|    2|   3|         3|         3|         3|         3|       161|
|  b|    1|   4|         4|         4|         3|         3|       161|
|  b|    0|   5|         5|         5|         2|         2|       161|
|  a|    4|   1|         1|         1|         2|         2|       170|
|  a|    3|   2|         2|         2|         1|         1|       170|
|  a|    2|   3|         3|         3|         1|         1|       170|
|  a|    1|   4|         4|         4|         0|         0|       170|
|  a|    0|   5|         5|         5|         0|         0|       170|
+---+-----+----+----------+----------+----------+----------+----------+
```

```bash
result.rdd.mapPartitionsWithIndex {
      case (partition, iter) =>
        iter.map {
          row =>
            (row.get(0).toString, row.get(1).toString, partition)
        }
    }.collect().sortBy(_._3).foreach(println)
```
```bash
(b,4,161)
(b,3,161)
(b,2,161)
(b,1,161)
(b,0,161)
(a,4,170)
(a,3,170)
(a,2,170)
(a,1,170)
(a,0,170)
```


### spark SQL分区是否对action与否不敏感?
只要进来的数据是稳定的，对action不敏感.
