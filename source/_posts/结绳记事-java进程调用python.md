---
title: 结绳记事-java进程调用python
date: 2019-09-10 10:41:14
tags:
---


## 结绳记事-java进程调用python

### java进程调用python
1)数据通过IO形式传输, 或者通过socket、rabitMQ也可，其实py4j和Jython就是这样的。
不过根据经验大量数据时还是尽量通过文件IO，注意删除临时文件夹即可
2)通过进程调用命令行执行

### scala/java到文件
```java
/**
  * scala端文件IO规则
  * ----
  * scala/java类型 => field
  * ----
  * Boolean(scala|java) => boolean
  * Byte(scala|java) => byte
  * Short(scala|java) => short
  * Int(scala|java) => int
  * Integer(java) => integer
  * Long(scala|java) => long
  * Float(scala|java) => float
  * Double(scala|java) => double
  * BigDecimal(scala) => bigdecimal
  * BigDecimal(java) => java.math.bigdecimal
  * String(scala|java) => string
  * java.sql.Date(java) => date
  * java.sql.Timestamp(java) => timestamp
  *
  * scala端写入规则
  * ----
  * 1)boolean | byte | short | int | integer | long |
  * float | double | bigdecimal | java.math.bigdecimal | string
  * 直接写就可以了
  * 2)Date | Timestamp以yyyy-MM-dd HH:mm:ss.SSS形式转为字符串写入
  *
  */
```

### 文件到python
``` python
    """
    根据字段类型dtype将字符串元素转为python对应的类型
    ----
    :param element: 文件中读取的元素
    :param dtype: 字段类型
    :return: 对应的python类型

    notes: field => python类型
    ----
    boolean => bool
    byte => int
    short => int
    int => int
    integer => int
    long => int
    float => float
    double => float
    bigdecimal => float
    java.math.bigdecimal => float
    string => string
    java.sql.Date(java) => datetime.datetime
    java.sql.Timestamp(java) => datetime.datetime

    other used logically
    ----
    @link [[output_miner]]
    """
```
### python到文件
```
    """
    将python对象转为字符串
    ----
    :param value: python对象
    :param na_rep: 缺失值对应的字符串
    :return: string

    notes: python对象输出到文件的规则
    ----
    None => na_rep
    bool => 转为小写的true/false
    int | float => 直接转为string
    numpy: int8|int16|int64|float8|float16|float32|float128 => 直接转为string
    datetime => 转为'yyyy-MM-dd HH:mm:ss.SSS'('%Y-%m-%d %H:%M:%S.%f')的字符串, 精确到小数点后6位
    string => 不能包含','
    """
```

### 文件到scala/java
``` scala
/**
  * scala端读取规则
  * ----
  * 1)boolean | byte | short | int | integer | long |
  * float | double | bigdecimal | java.math.bigdecimal | string
  * 读取为字符串, 如果是空字符串则转为null值, 否则转为对应类型,
  * 如果无法抛出异常
  * 2)Date | Timestamp,
  * 如果是空字符串则转为null值, 否则以yyyy-MM-dd HH:mm:ss.SSS或
  * yyyy-MM-dd HH:mm:ss.SSSSSS形式从字符串解析, 然后转为对应类型
  * 3)scala端读取无法区别null值和空字符串, 统一以null值输出
  */
```

### 其他问题
1)scala和java类型对应和转换, 会写一篇专门的文章
2)缺失值处理
3)缺失值和空字符串如何区分(暂未找到好方法)

### python的异常捕捉
通过traceback

### python脚本放置的位置
可以放在jar包中供调用, 放在resources目录下
```scala
  /**
    * 获取资源文件路径
    * ----
    * 适用于jar包调用和一般的工程引用 即获取resources路径
    * 适用于linux和windows环境
    * ----
    *
    * @param className 类名 仅适用于非静态类的类名
    * @return 资源的绝对路径
    */
  def getResourcesPath(className: String): String = {
    val constructor = Class.forName(className).getDeclaredConstructor()

    //把构造器私有权限放开 --针对单例
    constructor.setAccessible(true)
    //正常的获取实例方式
    val url = constructor
      .newInstance()
      .getClass
      .getProtectionDomain
      .getCodeSource
      .getLocation
      .toString

    getRidOfFileSystem(
      url2utf8(
        if (url == null) {
          throw new IllegalAccessException(s"在'$className'的资源目录中未找到")
        } else {
          url.toString
        })
    )
  }

  // 供选择的方案(选取了第二种, 同时为了保证单例(scala中常见的object)能执行将构造器方法开放, 注意会有单例攻击):
  // ----
  //  Class.forName(className)
  //    .newInstance()
  //    .getClass
  //    .getClassLoader
  //    .getResource(className.replace(".", "/") + ".class")
  // windows IDE file:/E:/company/studio/算法管理/项目/UE高级算法/pythonGateWay/v1.0/target/classes/com/sefon/miner/algorithm4ue/Test$.class
  // windows jar jar:file:/C:/Users/datashoe/.m2/repository/com/sefon/miner/pythonGateWay/1.0/pythonGateWay-1.0.jar!/com/sefon/miner/algorithm4ue/Test$.class
  // linux jar jar:file:/usr/local/pythonGateWay-1.0.jar!/com/sefon/miner/algorithm4ue/Test$.class
  // ----
  //  Class.forName(className)
  //    .newInstance()
  //    .getClass
  //    .getProtectionDomain
  //    .getCodeSource
  //    .getLocation
  //    .toString
  // windows IDE file:/E:/company/studio/算法管理/项目/UE高级算法/pythonGateWay/v1.0/target/classes/
  // windows jar file:/C:/Users/datashoe/.m2/repository/com/sefon/miner/pythonGateWay/1.0/pythonGateWay-1.0.jar
  // linux jar file:/usr/local/pythonGateWay-1.0.jar
  // ----
  //  Class.forName(className)
  //    .newInstance()
  //    .getClass
  //    .getResource("")
  //    .toString
  // windows IDE file:/E:/company/studio/算法管理/项目/UE高级算法/pythonGateWay/v1.0/target/classes/
  // windows jar file:/E:/company/studio/算法管理/项目/UE高级算法/algorithm4ue/v1.0/target/test-classes/
  // linux file:/opt/spark-2.4.0/conf/
```


### waitFor阻塞的问题
1)试了调节jvm堆栈无效，发现去掉一些打印有效，去掉过多的`try ... except ...`有效
2)看了篇博客, 输入流和异常流都是向缓冲区写数据，如果写满了会阻塞waitFor
>ProcessBuilder.start() 和 Runtime.exec 方法创建一个本机进程，并返回 Process 子类的一个实例，该实例可用来控制进程并获得相关信息。Process 类提供了执行从进程输入、执行输出到进程、等待进程完成、检查进程的退出状态以及销毁（杀掉）进程的方法。 
创建进程的方法可能无法针对某些本机平台上的特定进程很好地工作，比如，本机窗口进程，守护进程，Microsoft Windows 上的 Win16/DOS 进程，或者 shell 脚本。
创建的子进程没有自己的终端或控制台。它的所有标准 io（即 stdin、stdout 和 stderr）操作都将通过三个流 (getOutputStream()、getInputStream() 和 getErrorStream()) 重定向到父进程。父进程使用这些流来提供到子进程的输入和获得从子进程的输出。
因为有些本机平台仅针对标准输入和输出流提供有限的缓冲区大小，如果读写子进程的输出流或输入流迅速出现失败，则可能导致子进程阻塞，甚至产生死锁。

scala代码
```
  /**
    * 处理process输出流和错误流，防止进程阻塞
    * 在process.waitFor();前调用
    *
    * @param process 进程
    */
  private def dealWithStream(process: Process, command: String = ""): Unit = {
    if (process != null) {
      new Thread {
        override def run(): Unit = {
          println(s"执行进程'$command'的输出信息")
          // 处理InputStream
          val in = new BufferedReader(new InputStreamReader(process.getInputStream))
          var line = in.readLine()
          while (line != null) {
            println("输出流信息:" + line)
            line = in.readLine()
          }

          in.close()
        }
      }.start()

      new Thread {
        override def run(): Unit = {
          println(s"执行进程'$command'的错误流信息")
          val err = new BufferedReader(new InputStreamReader(process.getErrorStream))
          var line_err = err.readLine()
          while (line_err != null) {
            println("错误流信息:" + line_err)
            line_err = err.readLine()
          }

          err.close()
        }
      }.start()
    }

  }



    val runtime = Runtime.getRuntime
    val process = runtime.exec(command)

    class ProcessHold(process: Process) extends Thread {
      override def run(): Unit = {
        var i = 0
        while (process.isAlive) {
          if (i >= 10) {
            throw new RuntimeException("执行python算法时关闭进程异常.")
          }
          i = i + 1

          process.destroy()
        }
      }
    }

    def closeProcess(process: Process): Unit = {
      new ProcessHold(process).start()
    }

    dealWithStream(process, command)
    val i = process.waitFor()

    if (i != 0) {
      closeProcess(process)
      throw new RuntimeException(s"执行python命令'$command'出现异常") //Array(commands)
    } else {
      // 要求目录结构和开头注释中的一致
      //      datas = readDatas(rootUrl, message)
      closeProcess(process)
    }

```


参考资料:
[1]https://www.cnblogs.com/baiqjh/articles/2647551.html