---
title: spark拾遗之缺失值处理
date: 2019-05-13 19:40:00
tags:
---

## spark拾遗之缺失值处理

### 如何在任意类型的数据中构造出null值
1.方法1 通过udf将本来不是null的数据转为null
``` scala
// udf 中scalaUDF中有自动的Option类转换convert  org.apache.spark.sql.catalyst.CatalystTypeConverters
def createToCatalystConverter(dataType: DataType): Any => Any = {
    if (isPrimitive(dataType)) {
      // Although the `else` branch here is capable of handling inbound conversion of primitives,
      // we add some special-case handling for those types here. The motivation for this relates to
      // Java method invocation costs: if we have rows that consist entirely of primitive columns,
      // then returning the same conversion function for all of the columns means that the call site
      // will be monomorphic instead of polymorphic. In microbenchmarks, this actually resulted in
      // a measurable performance impact. Note that this optimization will be unnecessary if we
      // use code generation to construct Scala Row -> Catalyst Row converters.
      def convert(maybeScalaValue: Any): Any = {
        if (maybeScalaValue.isInstanceOf[Option[Any]]) {
          maybeScalaValue.asInstanceOf[Option[Any]].orNull
        } else {
          maybeScalaValue
        }
      }
      convert
    } else {
      getConverterForType(dataType).toCatalyst
    }
  }
```
2.方法2 通过Seq[Tuple] + Option创建
``` scala
val testData1 = Array(
      ("2017/02/10 00:00:00", Some(1), 2),
      ("2017/02/08 01:00:01", Some(1), 2),
      ("2017/3/1 04:00:02", Some(1), 2),
      ("2017/4/10 00:15:03", Some(1), 2),
      (null, Some(1), 2),
      ("2017/04/20 07:20:05", Some(1), 2),
      ("2017/04/30 08:01:06", Some(1), 2),
      ("2017/04/30 09:11:06", Some(2), 2),
      ("2017/04/30 16:01:06", Some(2), 2),
      ("2017/06/10 13:01:06", Some(2), 2),
      ("2017/08/10 00:00:00", Some(2), 2),
      ("2017/08/18 01:00:01", Some(1), 2),
      ("2017/11/1 04:00:02", Some(1), 2),
      ("2017/12/31 00:15:03", Some(1), 2),
      ("2017/04/10 06:20:04", Some(1), 2),
      ("2018/01/1 07:20:05", Some(1), 2),
      ("2018/02/19 13:01:06", Some(1), 2),
      ("2018/03/2 13:01:06", Some(2), 2),
      ("2018/03/9 13:01:06", Some(2), 2),
      ("2018/04/1 13:01:06", None, 2)
    )

    import org.apache.spark.sql.Row
    val data = spark.createDataFrame(testData1).toDF("time", "id", "value")

    data.show()
    data.printSchema()
```
3.方法3 
``` scala
val testData2 = Array(
      Row("2017/02/10 00:00:00", 1, 2)),
      Row("2017/02/08 01:00:01", 1, 2)),
      Row("2017/3/1 04:00:02", 1, 2)),
      Row("2017/4/10 00:15:03", 1, 2)),
      Row(null, 1, 2)),
      Row("2017/04/20 07:20:05", 1, 2)),
      Row("2017/04/30 08:01:06", 1, 2)),
      Row("2017/04/30 09:11:06", 2, 2)),
      Row("2017/04/30 16:01:06", 2, 2)),
      Row("2017/06/10 13:01:06", 2, 2)),
      Row("2017/08/10 00:00:00", 2, 2)),
      Row("2017/08/18 01:00:01", 1, 2)),
      Row("2017/11/1 04:00:02", 1, 2)),
      Row("2017/12/31 00:15:03", 1, 2)),
      Row("2017/04/10 06:20:04", 1, 2)),
      Row("2018/01/1 07:20:05", 1, 2)),
      Row("2018/02/19 13:01:06", 1, 2)),
      Row("2018/03/2 13:01:06", 2, 2)),
      Row("2018/03/9 13:01:06", 2, 2)),
      Row("2018/04/1 13:01:06", 2, 2)),
      Row("2018/04/1 13:01:06", null, 2)
    )

    val data1 = spark.createDataFrame(
      spark.sqlContext.sparkContext.parallelize(testData2),
      StructType(
        Array(
          StructField("time", StringType),
          StructField("id", IntegerType, nullable = true),
          StructField("time", IntegerType)
        )
      )
    )

    data1.show()
    data1.printSchema()
```