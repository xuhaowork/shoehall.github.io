---
title: '有序回归[Categorical Regression]基于哑变量的实现'
date: 2019-04-08 14:28:04
tags:
---
## 有序回归[Categorical Regression]基于哑变量的实现

### 材料
/workplace/studio/papers/glm/categorical regression/

### 代码
``` r
library(MASS)

xb <- c(1, 1, 1, 1, 1, 1, 0, 0, 0, 0, 0, 0)
lf <- c(1, 1, 1, 0, 0, 0, 1, 1, 1, 0, 0, 0)
lx <- c(1, 2, 3, 1, 2, 3, 1, 2, 3, 1, 2, 3)
ps <- c(16, 5,6, 6, 7, 19, 5, 2, 7, 1, 0, 10)
table <- data.frame(xb, lf, lx, ps)

responseLeves <- 2
responseId <- 3
rowNums <- dim(table)[1]
colNums <- dim(table)[2]


f <- function(x){
  lsData <- list()
  zero <- rep(0, responseLeves)
  x3 <- x[responseId]
  for(i in 1 : responseLeves){
    zero[i] <- 1
    x[responseId] <- x3 > i
    lsData[[i]] <- append(x, zero)
  }
  lsData
}

data <- apply(table, 1, f)

v <- c()
for(j in 1:rowNums){
  for(i in 1:responseLeves){
    v <- append(v, as.vector(data[[j]][[i]]))
  }
}

transformData <- matrix(data = v, nrow = rowNums*responseLeves,
                        ncol = colNums + responseLeves, 
                        byrow = TRUE, dimnames = NULL)

transformData <- as.data.frame(transformData)
colnames(transformData) <- c("xb", "lf", "lx", "ps", "oneVsTwo", "twoVsThree")

table
transformData


polr.model <- polr(as.ordered(lx) ~ xb + lf, Hess = T, data = table)
summary(polr.model) # 输出回归系数 
# xb -1.319
# lf -1.797
# 1|2 -2.6672
# 2|3 -1.8128


glm.model <- glm(lx ~ xb + lf + twoVsThree, 
                 family = binomial(), data = transformData)
summary(glm.model)
```