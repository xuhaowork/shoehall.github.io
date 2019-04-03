
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
```bash
# mkdir <your github>.github.io
# cd <your github>.github.io # 保证该目录为空
# hexo init # 初始化hexo, 创建基本的文件（如果是将hexo目录clone或拷贝到本地则不需要该步）
```
### 5.npm依赖下载(如果是clone下来的项目则不需要该步)
``` bash
hexo init # 可能会有warning, 不管
npm install # 下载依赖, cnpm install试了一下发现不行, 改为npm install可以了
npm install --save hexo-deployer-git # 下载deploy git的支持
```
### 6.启动server
hexo server # 启动hexo服务
查看本地pages（http://localhost:4000/）的编译效果, 会生成一个hello-world界面, 里面有一篇hello-world.md文档, 介绍对应的功能.

### 7.写文章
我们看下hello-world的文章结构
1）进入source的_posts中
2）可以看到hello-world.md的markdown
注意由--- ---包起来的front matter, 其中title为主页中显示的标题, categories和tags分别是分类和标签(这里这两个都是后续加上去的, 原始的没有)
![](2019-04-02-10-12-27.png)
3）之后是正文内容, 就是一般的markdown语法了

1)仿照hello-world写一篇文章your page, 可以执行hexo n "your page", 此时会在posts目录下生成your page.md文件(如果配置资源管理目录的话还会生成同名文件夹)
2)hexo generate
3)hexo server
4)进入"http://localhost:4000/"查看效果
以后步骤大致如上，进行修改后进行本地编译就执行2/3步
至此本地搭建的主干功能已经全了, 还有一些功能参考[官方参考文档](https://hexo.io/docs)
### 8.添加tags/categories/about
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
### 9.上传图片链接等
上传图片方式：
1.可以传到云端, 以云端地址作为绝对地址访问图片
如GeoSpark的logo(https://raw.githubusercontent.com/DataSystemsLab/GeoSpark/master/GeoSpark_logo.png)
将地址写在链接位置
``` md
![GeoSpark](https://raw.githubusercontent.com/DataSystemsLab/GeoSpark/master/GeoSpark_logo.png)
```
效果如下:
![GeoSpark](https://raw.githubusercontent.com/DataSystemsLab/GeoSpark/master/GeoSpark_logo.png)
该方法的好处是你也可以其他地方用到该图片而不用更改代码(比如csdn/简书等)。

2.将图片放到本地仓库（当然也要上传的, 不过仅仅放在_post下是不行的）, 添加相对地址
1)安装hexo-asset-image
``` bash
npm install https://github.com/CodeFalling/hexo-asset-image --save
```
注意有可能会有bug，此时/node_modules/hexo-asset-image/index.js替换为如下内容:
``` js
'use strict';
var cheerio = require('cheerio');

// http://stackoverflow.com/questions/14480345/how-to-get-the-nth-occurrence-in-a-string
function getPosition(str, m, i) {
  return str.split(m, i).join(m).length;
}

var version = String(hexo.version).split('.');
hexo.extend.filter.register('after_post_render', function(data){
  var config = hexo.config;
  if(config.post_asset_folder){
    	var link = data.permalink;
	if(version.length > 0 && Number(version[0]) == 3)
	   var beginPos = getPosition(link, '/', 1) + 1;
	else
	   var beginPos = getPosition(link, '/', 3) + 1;
	// In hexo 3.1.1, the permalink of "about" page is like ".../about/index.html".
	var endPos = link.lastIndexOf('/') + 1;
    link = link.substring(beginPos, endPos);

    var toprocess = ['excerpt', 'more', 'content'];
    for(var i = 0; i < toprocess.length; i++){
      var key = toprocess[i];
 
      var $ = cheerio.load(data[key], {
        ignoreWhitespace: false,
        xmlMode: false,
        lowerCaseTags: false,
        decodeEntities: false
      });

      $('img').each(function(){
		if ($(this).attr('src')){
			// For windows style path, we replace '\' to '/'.
			var src = $(this).attr('src').replace('\\', '/');
			if(!/http[s]*.*|\/\/.*/.test(src) &&
			   !/^\s*\//.test(src)) {
			  // For "about" page, the first part of "src" can't be removed.
			  // In addition, to support multi-level local directory.
			  var linkArray = link.split('/').filter(function(elem){
				return elem != '';
			  });
			  var srcArray = src.split('/').filter(function(elem){
				return elem != '' && elem != '.';
			  });
			  if(srcArray.length > 1)
				srcArray.shift();
			  src = srcArray.join('/');
			  $(this).attr('src', config.root + link + src);
			  console.info&&console.info("update link as:-->"+config.root + link + src);
			}
		}else{
			console.info&&console.info("no src attr, skipped...");
			console.info&&console.info($(this));
		}
      });
      data[key] = $.html();
    }
  }
});
```
2)在_config.yml中添加如下内容:
``` md
# Writing
post_asset_folder: true # 启动资源管理文件夹
```
注意如果引用的是theme的yml需要在theme中进行配置。
3)通过hexo new \<your pages\>生成新的页面
hexo n "hexoImage3"
如果上述内容起作用此时在_posts中会生成两个文件一个hexoImage3.md另一个hexoImage3文件夹.
4)引用的图片放入生成的文件夹, 并在生成的md文件中加入索引
``` md
![](image.png)
```
注：其他的音频视频mv/mp3等与之类似。

### 10.发布到github以及如何多个终端同时开发
1）通过deploy可以发布pages到github, yml文件中有deploy的github对应仓库信息。
```md
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: https://github.com/... # 此处应该填自己的github.io仓库
  branch: master
```
2）注意：*hexo pages的deploy会有点一般的github repository不一致的地方*
* deploy只会将生成的public文件夹放到指定的github上(而不是基于仓库中的文件), 会产生本地仓库中的其他文件无法同步到github上的问题。
* 解决方式：通过分支管理。构建一个分支hexo管理source文件（只是source）, master分支管理deploy
a. 发布到github
``` bash
# 创建仓库并进行第一次commit生成master分支
git init # 在该工作空间中创建git本地仓库
echo "# shoehall.github.io, source branch: master, deploy branch: hexo" >> README.md
git add README.md # 
git commit -m "first commit" # 第一次commit后master分支才会创建, 此时才能创建hexo分支
```
``` bash
# 创建hexo分支并上传hexo文件
git branch hexo  # 新建hexo分支
git checkout hexo  # 切换到hexo分支上
git remote add origin git@github.com:<yourname>/<yourname>.github.io.git  # 与Github项目连接 这里yourname代表你的昵称
git add --all # 将hexo仓库中的文件add进来
git commit -m "hexo分支 hexo仓库文件第一次上传" # commit
git push origin hexo  # push到Github项目的hexo分支上
```
如果弄好了可以发布
```bash
hexo deploy # 此时deploy是发布到master分支下
```
说明：此时hexo对应仓库中除了ignore的文件外的目录, 而master对应deploy后的public目录.

b. 在其他终端进行首次开发
```bash
# 环境准备 安装rpm/git等与前面相同
# clone hexo分支
git clone -b hexo git@github.com:yourname/yourname.github.io.git # yourname填入你的github
cd yourname.github.io # 进入克隆的目录
npm install # 安装依赖, 由于hexo中包含对应的文件不需要再hexo init
hexo new post "new blog name" # 此处表示进行一些操作, 也可以是其他
git add --all
git commit -m "新的操作"
git push origin hexo  # 更新hexo分支
hexo deploy # 发布（到master）
```
c. 多个终端同步开发的流程
指进行首次开发之后，每次开发前的应有流程
``` bash
git pull origin hexo  # 先pull完成本地与远端的融合
hexo new post " new blog name" # 进行某些操作
git add --all
git commit -m "XX"
git push origin hexo
hexo d -g
```
============================== 分割线 ==============================
* 以下内容为个性部分
### 11.更换theme
网络上有很多themes
1)进入themes文件夹下, 可以看到landscape目录这就是hexo初始化的目录
2)进入[hexo主题页面](https://hexo.io/themes/)选择一个主题(注意预览和下面链接的区别), git clone下来, 注意可以修改下原作者一些个性的设置(比如_config.yml中的github链接, 个性介绍等等)
3)在_config.yml中更改theme设置, 将landspace改为下载的theme
``` bash
theme: theme-ad
```
注意：更换theme后theme也有yml文件, 其中有一些设置（比如post_asset_folder）, 注意考虑到
### 12.markdown/pages的编辑器
最基本的文本编辑器起始都可以, 感受好一点的是VS Code
1)下载VS Code
2)安装markdown插件(markdown all in one, markdown PDF, markdown previewn enhance), 安装hexo插件(hexo-one, hexopost head generator), 安装截图的粘贴插件(可以直接截图粘贴连接到md)paste image
![](p1.png)
3)配置下bash为terminal这样不用来回切换目录
找到bash的路径（一般为C:\Program Files\Git\bin, 不记得可以去环境变量中找）
在setting中设置
![](p2.png)
其他的几个关于windows的shell也搞搞, 包括workspace settings的
ctrl+shift+C看下效果, 这是弹出窗口
ctrl + `看下效果这是IDE
### 13.绑定自己的域名
1)首先要由一个自己的域名, 可以去买一个(阿里云, 腾讯云等等之类的, 要实名认证后才能正常使用)， www.yoursite.cn(yoursite为你注册的域名), 注意等实名认证和审核通过后才能正常使用。状态需要为:审核通过.
2)在source目录下创建CNAME文件(无后缀), 在其中输入www.yoursite.cn
3)在github对应仓库的domain中输入www.yoursite.cn
4)进入域名解析的设置（以阿里云为例），新建两个解析：记录类型为CNAME, 记录值为'www.yourname.github.io'，主机记录为@和www。
![](p3.png)
此处相当于将你的'www.yourname.github.io'绑定到你注册的'www.yoursite.cn'
5)发布后，等会查看是否生效
