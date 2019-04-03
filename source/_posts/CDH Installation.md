---
title: CDH5.14安装————基于centos6.7
categories: 
  - 运维
tags:
  - CHD
  - SPAKR
  - Linux
---

## 创建一个自己的Git Pages

# 安装paste image
![](2019-04-02-09-46-50.png)


### 1.环境准备
``` bash
# 环境: windows7

# 注册github账号
# 安装git(git bash)
# 配置ssh免密远程登录(此处也可省略)
# 需要一个能编辑markdown的文本编辑器(notepad, intellij, VSCode等都可以)
```

### 2.安装node js
注意：这里git bash其实充当了windows下的linux bash命令窗口的作用. 有些操作在cmd下同样可以.
1) 下载并安装[node.js](http://nodejs.org/)
2) 安装node js, 注意配置环境变量
3) 打开git bash（或cmd）, 查看npm是否起效
```
npm -v
```

### 3.部署hexo
```
cnpm install -g hexo-cli
```
有时候国外的cnpm用不了需要换为淘宝云, bash中输入：
```
alias cnpm="npm --registry=https://registry.npm.taobao.org \
--cache=$HOME/.npm/.cache/cnpm \
--disturl=https://npm.taobao.org/dist \
--userconfig=$HOME/.cnpmrc"
```

### 4.创建工作目录
创建一个空目录。
也可以通过如下方法，直接创建到github.io仓库中:
1) 创建github.io仓库
2) clone到本地

### 5.hexo项目初始化

``` bash
hexo init # 可能会有warning, 不管
npm install # cnpm install试了一下发现不行, 改为npm install可以了
hexo server # 启动hexo服务
```
查看本地[](http://localhost:4000/)


``` bash
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

### Deploy to remote sites

``` bash
$ hexo deploy
```

More info: [Deployment](https://hexo.io/docs/deployment.html)
