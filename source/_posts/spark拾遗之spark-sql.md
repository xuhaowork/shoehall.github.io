---
title: spark拾遗之spark sql
date: 2019-04-26 21:06:20
tags:
    - Spark
    - SQL
---

## spark拾遗之spark sql

### spark一些好用的内置函数用sql实现
用sql实现的好处是：很多应用都支持SQL语言，因此如果能够熟练使用spark sql可以快速解决数据预处理的一些问题. 尤其是一些简易现场(没有搭建开发环境或者是其他人的现场)
1.concat和concat_ws
concat
示例:
``` bash
# 表aa
+---+
| ff|
+---+
|  0|
|  1|
|  2|
|  3|
|  4|
|  5|
|  6|
|  7|
|  8|
|  9|
+---+
# aa.createOrReplaceTempView("aa")
```
``` sql
select concat(ff, 's') as new_ff from aa
select concat_ws('s', ff, '') as new_ff from aa
```
concat是在ff列后拼接一列's'
concat_ws是在ff列和''列之间以字符串's'拼接

``` bash
+------+
|new_ff|
+------+
|    0s|
|    1s|
|    2s|
|    3s|
|    4s|
|    5s|
|    6s|
|    7s|
|    8s|
|    9s|
+------+

# bb.createOrReplaceTempView("bb")
```
2.regexp_extract
regexp_extract利用正则表达式中的分组提取字符串
``` sql
select regexp_extract(new_ff, '[0-9]', 0) as ff from bb
select regexp_extract(new_ff, '([0-9])', 0) as ff from bb
select regexp_extract(new_ff, '([0-9])', 1) as ff from bb
```
``` bash
+----+
|  ff|
+----+
|   0|
|   1|
|   2|
|   3|
|   4|
|   5|
|   6|
|   7|
|   8|
|   9|
+----+
```

``` sql
select regexp_extract(new_ff, '([0-9])(0-z)', 2) as ff from bb
```
```
+---+
| ff|
+---+
|  s|
|  s|
|  s|
|  s|
|  s|
|  s|
|  s|
|  s|
|  s|
|  s|
+---+
```

3.regexp_replace
将匹配的正则表达式替换为对应的内容
示例1: 直接替换字符串
``` sql
select regexp_replace(new_ff, '[0-9]', 'some ') as cc_ff from bb
```
``` bash
+------+
| cc_ff|
+------+
|some s|
|some s|
|some s|
|some s|
|some s|
|some s|
|some s|
|some s|
|some s|
|some s|
+------+
```

示例2: 分组匹配
``` sql
select regexp_replace(new_ff, '([0-9])', '$1 seond : $1') as cc from bb
```

``` bash
+------------+
|          cc|
+------------+
|0 seond : 0s|
|1 seond : 1s|
|2 seond : 2s|
|3 seond : 3s|
|4 seond : 4s|
|5 seond : 5s|
|6 seond : 6s|
|7 seond : 7s|
|8 seond : 8s|
|9 seond : 9s|
+------------+
```
4.rank, dense_rank和row_number标号
``` scala
val ScoreDetailDF = sparkSession.createDataFrame(Seq(
      ("王五", "一年级", 98),
      ("李四", "一年级", 100),
      ("小民", "二年级", 90),
      ("小明", "二年级", 100),
      ("张三", "一年级", 100),
      ("小芳", "二年级", 95)
    )).toDF("name", "grade", "score")

// SparkSQL 方法实现
ScoreDetailDF.createOrReplaceTempView("scoredetail")
```
``` sql
select * , rank() over (partition by grade order by score desc) as rank, dense_rank() over (partition by grade order by score desc) as dense_rank, row_number() over (partition by grade order by score desc) as row_number from scoredetail
```
``` bash
+------+-------+-----+----+----------+----------+
|name  |  grade|score|rank|dense_rank|row_number|
+------+-------+-----+----+----------+----------+
|  李四|  一年级|  100|   1|         1|         1|
|  张三|  一年级|  100|   1|         1|         2|
|  王五|  一年级|   98|   3|         2|         3|
|  小明|  二年级|  100|   1|         1|         1|
|  小芳|  二年级|   95|   2|         2|         2|
|  小民|  二年级|   90|   3|         3|         3|
+------+-------+-----+----+----------+----------+
```
5.select语句创建测试数据



