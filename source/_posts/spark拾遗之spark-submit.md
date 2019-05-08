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


