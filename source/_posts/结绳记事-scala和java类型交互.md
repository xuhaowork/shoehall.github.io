---
title: 结绳记事-scala和java类型交互
date: 2019-08-29 16:29:27
tags:
---

## scala和java类型互转

### BigDecimal和BigInt
``` scala
// scala 2 java
val sb = scala.math.BigDecimal(12345)
println(sb)
val jb = sb.bigDecimal
println(jb)
// java 2 scala
val sclb = new BigDecimal(jb, BigDecimal.defaultMathContext)
println(sclb)

// scala 2 java
val sbi = scala.math.BigInt(1234)
val jbi = sbi.bigInteger
println(jbi)
// java 2 scala
val sclbi = new BigInt(jbi)
println(sclbi)
```

### 