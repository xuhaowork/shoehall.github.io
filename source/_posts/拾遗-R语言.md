---
title: 拾遗-R语言
date: 2019-04-11 13:15:54
tags:
---

## 拾遗-R语言

### easyGgplot2可以实现一个window多张图
### RStudio打印更多航
options(max.print=1000000)
可以解决getOption("max.print")的问题

### R中执行System命令
system("javac F://NoParameter.java")

### 同一个终端打开两个RStudio的问题
该问题出现在：本地win7装了一个RStudio，另外星环集群还有一个8887端口的RStudio网页版服务，结果打开了网页版之后，一些配置被修改了，导致安装包安装不上等一些问题。
猜测大概是因为：RStudio/bin/x86/ression.exe会修改IE配置，而网页版的和本地的不一致，导致被网页版修改后，RStudio下载出现“安全频道支持出错”之类的问题。

### RStudio中多行注释的问题
Crtl + Shift + c 

### R语言小波分析
/workplace/studio/pages/learning materials/wavelet/Wavelet Methods in Statistics with R

### R语言分类和回归方法度量-caret包
/workplace/studio/pages/papers/vignetteForCaret
