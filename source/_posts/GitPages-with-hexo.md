---
title: Git Pages With Hexo
categories: 
  - 管理工具
tags:
  - Git
---

## 创建一个自己的Git Pages

### 1. 环境准备
``` bash
# 环境: windows7

# 注册github账号
# 安装git(git bash)
# 配置ssh免密远程登录(此处也可省略)
# 需要一个能编辑markdown的文本编辑器(notepad, intellij, VSCode等都可以)
```

### 2.安装node js
1) 下载并安装[node.js](http://nodejs.org/)
2) 安装node js, 注意配置环境变量
3) 打开git bash（或cmd）, 查看npm是否起效
```
npm -v
```
注意：这里git bash其实充当了bash命令窗口的作用. 下文的操作有些其实用cmd或者手动操作有同样效果.

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
# mkdir <your github>.github.io
# cd <your github>.github.io # 保证该目录为空
# hexo init # 初始化hexo, 创建基本的文件（如果是将hexo目录clone或拷贝到本地则不需要该步）

### 5.npm服务(如果是clone下来的项目则不需要该步)

``` bash
hexo init # 可能会有warning, 不管
npm install # cnpm install试了一下发现不行, 改为npm install可以了
hexo server # 启动hexo服务
```
查看本地pages（http://localhost:4000/）的编译效果, 会生成一个hello-world界面, 里面有一篇hello-world.md文档, 介绍对应的功能.

### 6.hello-world
1）进入source的_posts中
2）可以看到hello-world.md的markdown
注意由--- ---包起来的front matter, 其中title为主页中显示的标题, categories和tags分别是分类和标签(这里这两个都是后续加上去的, 原始的没有)
![](2019-04-02-10-12-27.png)

3）之后是正文内容, 就是一般的markdown语法了

### 7.写文章
1)仿照hello-world写一篇文章
2)hexo generate
3)hexo server
4)进入"http://localhost:4000/"查看效果
以后步骤大致如上，进行修改后进行本地编译就执行2/3步
如果弄好了可以发布就执行
```bash
hexo deploy
```
至此主干功能已经全了, 还有一些功能参考[官方参考文档](https://hexo.io/docs)

### 8.上传图片链接等



============================== 分割线 ==============================
以下内容为个性部分
### 8.更换theme
网络上有很多themes
1)进入themes文件夹下, 可以看到landscape目录这就是hexo初始化的目录
2)进入[hexo主题页面](https://hexo.io/themes/)选择一个主题(注意预览和下面链接的区别), git clone下来, 注意可以修改下原作者一些个性的设置(比如_config.yml中的github链接, 个性介绍等等)
3)在_config.yml中更改theme设置, 将landspace改为下载的theme
``` bash
theme: theme-ad
```
4)重新重复7中所说的hexo步骤
### 9.添加tags/categories/about
1）生成对应的文件目录
添加tags
```bash
hexo new page tags
```
添加categories
```bash
hexo new page categories
```
添加about
```bash
hexo new page about
```
2）更改对应的'index.md'
此时source下会生成几个目录
![](2019-04-02-10-32-11.png)
进入目录打开其中的'index.md', 将其修改为如下形式:
```md
---
title: categories
date: 2019-04-01 19:27:10
type: "categories" 
---
```
这里"categories"是以其为例, 其他的名字需要对应为tags和about
3)进入文章，给文章添加tag和categories
```md
---
title: Git Pages With Hexo
categories: 
  - 管理工具
tags:
  - Git
---
```
### 10.绑定自己的域名
1)首先要由一个自己的域名, 可以去买一个(阿里云, 腾讯云等等之类的, 要实名认证后才能正常使用)， www.yoursite.cn
2)在source目录下创建CNAME文件(无后缀), 在其中输入www.yoursite.cn
3)在github对应仓库的domain中输入www.yoursite.cn, 此时仓库名字可以不以io结尾了可以换一个
4)待续

-------------------- 体验部分 --------------------

### 11.markdown/pages的编辑器
最基本的文本编辑器起始都可以, 感受好一点的是VS Code
1)下载VS Code
2)安装markdown插件(markdown all in one, markdown PDF, markdown previewn enhance), 安装hexo插件(hexo-one, hexopost head generator), 安装截图的粘贴插件(可以直接截图粘贴连接到md)paste image
![](2019-04-02-15-14-44.png)