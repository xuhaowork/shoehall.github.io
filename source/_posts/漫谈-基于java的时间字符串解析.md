---
title: 漫谈-基于java的时间字符串解析
date: 2019-06-15 00:51:29
category:
    -漫谈
tags:
    -时间
    -时间字符串
---

## 漫谈-基于java(scala)的时间字符串解析

### 1.一些概念
1)什么是时间字符串
2)时区
3)时令制度
4)当地属性
5)时间字符串可以表达的信息
6)时间字符串有多少种？


### 2.日期格式和字符集
1)年
y 将年份 (0-9) 显示为不带前导零的数字
yy 以带前导零的两位数字格式显示年份
yyy 以四位数字格式显示年份
yyyy 以四位数字格式显示年份

2)月
M 将月份显示为不带前导零的数字（如一月表示为 1）
MM 将月份显示为带前导零的数字（例如 01/12/01）
MMM 将月份显示为缩写形式（例如 Jan）
MMMM 将月份显示为完整月份名（例如 January）
```
一月 Jan January
二月 Feb February
三月 Mar March
四月 Apr April
五月 May May
六月 Jun June
七月 Jul July
八月 Aug August
九月 Sep September
十月 Oct October
十一月 Nov November
十二月 Dec December
```

3)星期
EEE 将日显示为缩写形式（例如 Sun）
EEEE 将日显示为全名（例如 Sunday）
```
星期一 Mon Monday
星期二 Tue Tuesday
星期三 Wed Wednesday
星期四 Thu Thursday
星期五 Fri Friday
星期六 Sat Saturday
星期天 Sun Sunday
```

4)日
d 将日显示为不带前导零的数字（如 1）
dd 将日显示为带前导零的数字（如 01）

5)小时
h 使用 12 小时制将小时显示为不带前导零的数字（例如 1:15:15 PM）
hh 使用 12 小时制将小时显示为带前导零的数字（例如 01:15:15 PM）
H 使用 24 小时制将小时显示为不带前导零的数字（例如 1:15:15）
HH 使用 24 小时制将小时显示为带前导零的数字（例如 01:15:15）

6)分钟
m 将分钟显示为不带前导零的数字（例如 12:1:15）
mm 将分钟显示为带前导零的数字（例如 12:01:15）

7)秒
s 将秒显示为不带前导零的数字（例如 12:15:5）
ss 将秒显示为带前导零的数字（例如 12:15:05）

S 显示秒的小数部分
SS 将精确显示到百分之一秒
SSS 将精确显示到千分之一秒

8)上午&下午
t 使用 12 小时制

中午之前任一小时显示大写的 A
中午到 11:59 PM 之间的任一小时显示大写的 P

tt 对于使用 12 小时制的区域设置

中午之前任一小时显示大写的 AM
中午到 11:59 PM 之间的任一小时显示大写的 PM

对于使用 24 小时制的区域设置，不显示任何字符

9)时区
z 显示不带前导零的时区偏移量
zz 显示带前导零的时区偏移量（例如 -08）
zzz 显示完整的时区偏移量（例如 -0800）

10)纪元
gg 显示时代/纪元字符串（例如 A.D.）

11)不同解析器自己的一些特殊设置
SimpleDateFormat XXX 1970-01-01T08:00:00.000+08:00 // RFC 3339
joda ZZ 1970-01-01T08:00:00.000+08:00

### SimpleDateFormat


### DateTimeFormatter


### DateTimeFormatter

### 时间字符串解析中的难点
1)很多人不能掌握时间字符串格式
2)变化太多样了
3)自动推断格式避免不了错解 07082007 可能是2017-08-07, 可能是2017-07-08
4)基于单条记录很难确定属性哪条数据, 因此基于规则

### 一些其他比较好的
自动解析时间字符串的 GO https://github.com/araddon/dateparse

### 附录


### 参考资料
[1] 时间字符串格式 https://www.jianshu.com/p/23aea01133e1
[2] SimpleDateFormat的API https://docs.oracle.com/javase/7/docs/api/java/text/SimpleDateFormat.html
[3] DateTimeFormatter的API https://docs.oracle.com/javase/8/docs/api/index.html
[4] Joda DateTimeFormatter的API https://www.joda.org/joda-time/apidocs/index.html
[5] itu https://github.com/ethlo/itu