---
title: 工具箱-mathpix snipping
date: 2019-04-09 10:08:25
tags:
---

## 工具箱 mathpix snipping
今天接触了一款及其强大易用的工具mathpix snipping, 可以将截图中的数学公式转为latex, 是数据挖掘工程师的利器.

### 下载安装
https://mathpix.com/

### 效果展示
打开一个文件
ctrl+alt+M截图
此时mathpix snapping会生成对应的latex公式
在markdown中输入公式（vscode中安装markdown enhangcing后可以$$$输入公式。

$\begin{aligned} f(x)=f\left(x_{0}\right)+f\left(x_{0}\right)\left(x-x_{0}\right)+\frac{f^{\prime}\left(x_{0}\right)}{2 !}\left(x-x_{0}\right)^{2}+\cdots+\frac{f^{(n)}\left(x_{0}\right)}{n !}\left(x-x_{0}\right)^{n}+R_{n} \end{aligned}$

注：mathpix将换行也识别出来了，由于markdown本身排版可能不一样因此可以通过增删换行'\\\ &'来重新排版。

### 结合其它工具将公式转为MathML code向Office Word中插入公式
示例, 利用在线工具:https://www.latex4technics.com/
```
# 1.利用mathpix snipping从图形中获得latex公式
# 2.进入https://www.latex4technics.com/, 复制公式
# 3.点击左上角compile
# 4.右下角的MathJax中右击 => show Math as => MathML Code
# 5.进入word, Alt + "+"插入公式, 将MathML Code复制到公式输入窗口, 粘贴为纯文本
# 6.调整下不一致的地方, 调整下行距(如果需要的话, 公式输入窗宽 => 段落 => 单倍行距)
```