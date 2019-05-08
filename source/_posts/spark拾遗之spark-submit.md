---
title: spark拾遗之spark-submit
date: 2019-04-26 15:42:07
tags:
    - Spark
---

## spark拾遗之spark-submit
### spark-shell配置参数
``` bash
# 设置一些系统参数
spark-shell --driver-memory 4g --driver-cores 4 --master local
```

``` bash
# 提交jar
spark-shell --jars lib/pkg1.jar,lib/pkg2.jar --driver-memory 4g --driver-cores 4 --master local
```

``` bash
# 来个高级版的提交jar -- 利用了管道符并且将find后的结果改为以','拼接
spark-shell --jars $(find lib/ -name '*.jar' | xargs echo | tr ' ' ',') --driver-memory 4g --driver-cores 4 --master local
```


### spark-submit配置参数
``` bash
spark-submit \
--master yarn \
--driver-memory 1G \
--executor-memory 1G --num-executors 1 \
--executor-cores 1 \
--jars $(find lib/ -name '*.jar' | xargs echo | tr ' ' ',') \
--class com.sefon.spark.ml.evaluation.RunMain # 还有点问题, 缺resource啥的
param1 param2 # param1为参数可以省略
```

