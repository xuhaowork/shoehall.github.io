---
title: 结绳记事-wolframe mathematica
date: 2019-04-09 18:33:32
tags:
---

## wolframe mathematica安装

### mathematica的安装
/workplace/studio/pages/tools/mathematica/

### wolframe语言
参考材料: /workplace/studio/pages/learning materials/mathematica/

Wolfram Mathematica学习
``` wolframe
执行
Shift enter

终止
Ctrl + .

前一个或前几个变量的值
%
%5
%11

清空缓存
Clear[“`*”]

方括号[]
表示输入参数，如： Sin[x]

圆括号()
表示分组，如(x + y)*2

精确到多少位有效数字
N[x, 10]
解方程
Solve[x^3 – 2^2 == 1]

展开多项式
Expand[(1 + x)^10]

绘图
Plot[{Sin[x], 2 + x}, {x, 0, 2*Pi}]

绘制点
Graphics[Point[{0.5,0.25}]]

绘制热力图
ContourPlot[x^2+y^2==1,{x,-1,1},{y,-1,1}]
求最小值
FindMinimum[(x-1)^2+(y-0.5)^2*2+Abs[x]+Abs[y],{x,0.8},{y,0.1}]

制作动画
Manipulate[Plot[aSin[b(x+c)],{x,0,2Pi},PlotRange→1],{b,1,20,Appearance→"Open"},{{a,1},0,1,Appearance→"Open"},{c,0,2Pi,Appearance→"Open"}]
```
