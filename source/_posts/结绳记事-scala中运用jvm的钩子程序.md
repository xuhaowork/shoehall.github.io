---
title: 结绳记事-scala中运用jvm的钩子程序
date: 2019-05-07 09:40:15
tags:
    - scala
    - java
    - 钩子
---

## 结绳记事-scala中运用jvm的钩子程序
```scala
 class Hold(input: String*) extends Thread {

      override def run(): Unit = {
        println("执行了钩子程序")
      }
    }

    val runtime = Runtime.getRuntime
    runtime.addShutdownHook(new Hold())

    println("开始执行")
    throw new Exception("抛出异常")

    /**
      * 钩子执行不了的情况
      * ----
      * 钩子是基于JVM的, 如果一个程序运行过程中导致了JVM挂掉了那么不会出现异常
      * 1)JVM内部发生错误, 可能还没有来得及触发钩子程序, JVM就挂掉了(JVM 发生内部错误, 有没有日志呢?)
      * 2)还有上面我们给出的那个信号表, 如果操作系统发送出上面的信号的话, 同样的, JVM没有执行钩子程序就退出了
      * 3)还有调用Runime.halt()函数也不会执行钩子程序
      */
```


