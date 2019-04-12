---
title: 工具箱-项目管理sbt
date: 2019-04-12 10:17:49
tags:
---

## 工具箱-项目管理之sbt

### 参考文献
sbt创建子模块
https://alvinalexander.com/scala/how-to-create-sbt-projects-with-subprojects
https://github.com/alvinj/SbtSubProjectsExample

``` scala
## sbt附带子项目
ThisBuild / version := "0.1"

//scalaVersion := "2.10.4"
ThisBuild / scalaVersion := "2.11.8"

//val sparkVersion = "1.5.2"
val sparkVersion = "2.0.0"
val log4jVersion = "2.6.1"

lazy val commonSettings = Seq(
    libraryDependencies ++= Seq(
        "org.apache.spark" %% "spark-core" % sparkVersion,
        "org.apache.spark" %% "spark-sql" % sparkVersion,
        "org.apache.spark" %% "spark-mllib" % sparkVersion,

        "org.deeplearning4j" % "deeplearning4j-core" % "0.8.0",
        //"org.deeplearning4j" % "dl4j-spark" % "0.8.0",
        "org.nd4j" % "nd4j-native-platform" % "0.8.0",
        //"com.github.karlhigley" %% "spark-neighbors" % "0.2.2",
        "com.github.haifengl" %% "smile-scala" % "1.3.1",

        "org.vegas-viz" %% "vegas" % "0.3.11",
        "org.vegas-viz" %% "vegas-spark" % "0.3.11",

        "com.twelvemonkeys.imageio" % "imageio-core" % "3.3.2",
        "com.twelvemonkeys.imageio" % "imageio-jpeg" % "3.3.2",

        "org.scalanlp" %% "breeze" % "0.11.2",
        "org.scalanlp" %% "breeze-viz" % "0.11.2",

        "org.apache.poi" % "poi" % "3.14",
        "org.apache.poi" % "poi-ooxml" % "3.14",
        "org.apache.poi" % "poi-ooxml-schemas" % "3.14",
        "org.apache.poi" % "poi-scratchpad" % "3.14",

        "com.google.code.gson" % "gson" % "2.2.4",
        "mysql" % "mysql-connector-java" % "5.1.12",
        "de.lmu.ifi.dbs.elki" % "elki" % "0.7.1",
        "com.hankcs" % "hanlp" % "portable-1.6.3"
        //"ml.dmlc" % "xgboost4j" % "0.80"
    )
)

lazy val zzjz = (project in file("."))
        .aggregate(zzjz_basic, zzjz_utils)
        .dependsOn(zzjz_basic, zzjz_utils)
        .settings(
            name := "zzjz",
            target := file("out/" + name.value + "/target"),
            commonSettings,
            update / aggregate := false,
            unmanagedBase := baseDirectory.value / "libs",
            //scalacOptions in ThisBuild ++= List("-Yresolve-term-conflict:package"),
            retrieveManaged := true
        )

lazy val zzjz_others = (project in file("zzjz-others"))
        .aggregate(zzjz_basic, zzjz_utils)
        .dependsOn(zzjz_basic, zzjz_utils)
        .settings(
            name := "zzjz-others",
            target := file("out/" + name.value + "/target"),
            commonSettings
        )

lazy val zzjz_basic = (project in file("zzjz-basic"))
        .settings(
            name := "zzjz-basic",
            target := file("out/" + name.value + "/target"),
            commonSettings
        )

lazy val zzjz_utils = (project in file("zzjz-utils"))
        .aggregate(zzjz_basic)
        .dependsOn(zzjz_basic)
        .settings(
            name := "zzjz-utils",
            target := file("out/" + name.value + "/target"),
            commonSettings
        )
```
