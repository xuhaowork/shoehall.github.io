---
title: 漫谈-spark lazy模式+闭包+可变变量编程要避开的一些坑
date: 2019-06-20 11:41:45
tags:
---

## 漫谈-spark lazy模式+闭包+可变变量编程要避开的一些坑

### 从一个简单的例子说起
```
val rdd = spark.sparkContext.parallelize(
    Seq(0, 1, 2, 3, 4, 5, 6)
)
    
var m = 0
val rdd1 = rdd.map {
    i => i * m
}
println(rdd1.collect().mkString(","))

m = 1
println(rdd1.collect().mkString(","))
```
```bash
0,0,0,0,0,0,0
0,1,2,3,4,5,6
```

### 解说
1)spark lazy模式: RDD中存储的是计算过程, 其中调用了函数(i: Int) => i * m
2)m是作为闭包自动传递的
3)第一次计算相当于map中调用函数(i: Int) => i * 0, 第二次相当于调用(i: Int) => i * 1


### 实际应用场景
实际中最常用的计算场景是while循环时的赋值
``` scala
var rdd = spark.sparkContext.parallelize(
    Seq(0, 1, 2, 3, 4, 5, 6)
)

var m = 1
while (m <= 3) {
    println(s"==========第'$m'次循环==========")
    println(rdd.collect().mkString(","))
    rdd = rdd.map {
        i => i * m
    }
    println(rdd.collect().mkString(","))

    m += 1
}
```

``` bash
==========第'1'次循环==========
0,1,2,3,4,5,6
0,1,2,3,4,5,6
==========第'2'次循环==========
0,2,4,6,8,10,12 -- 此处应该为0,1,2,3,4,5,6
0,4,8,12,16,20,24
==========第'3'次循环==========
0,9,18,27,36,45,54 --此处应该为0,4,8,12,16,20,24
0,27,54,81,108,135,162
```

### 怎样避免
``` scala
var rdd = spark.sparkContext.parallelize(
    Seq(0, 1, 2, 3, 4, 5, 6)
)

var m = 1
while (m <= 3) {
    val n = m
    println(s"==========第'$n'次循环==========")
    println(rdd.collect().mkString(","))
    rdd = rdd.map {
        i => i * n
    }
    println(rdd.collect().mkString(","))

    m += 1
}
```

``` bash
==========第'1'次循环==========
0,1,2,3,4,5,6
0,1,2,3,4,5,6
==========第'2'次循环==========
0,1,2,3,4,5,6
0,2,4,6,8,10,12
==========第'3'次循环==========
0,2,4,6,8,10,12
0,6,12,18,24,30,36
```

### 其他的一些常用的情况
RDD中涉及到对象作为可变变量, 比如Map类型 => 要深拷贝








