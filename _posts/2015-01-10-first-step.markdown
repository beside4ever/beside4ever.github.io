---
layout: post
title: "利用Github Pages在Windows平台部署Octopress"
date: 2015-01-10 03:02:43 +0800
comments: true
categories: Octopress
---
## 前言 ##
经过新浪博客——WordPress——至今的三重进化，时间来到了2015年，荒废了两年的Blog大业再次被想起，于是经过一天的配置，本博客终于展现在你的眼前。

本博客基于Octopress+Github Pages搭建，可以搭建全免费、稳定运行的个人博客。
本文将简述在Windows平台（Win7 64bit）上搭建这套系统的全流程和要点。

在继续看本文之前，请先确定你具有**折腾**的精神。
<!--more-->
## 目录 ##

1. 基础介绍
2. 安装
	1. Ruby环境
	2. Octopress
	3. 测试
3. 配置
	1. 基本配置
	2. 中国特色
	3. 中文化
3. 撰写日志
4. 发布
	1. 申请空间
	2. 本地配置
	3. 自定域名
4. 总结

## 基础介绍 ##
[Octopress](http://octopress.org/)是一个基于Ruby语言的开源静态网站框架，所谓静态，是指网站的所有内容都是生成好的静态HTML，不含任何后台处理程序，也没有数据库。这样的好处是网站的加载速度会非常快，整体程序规模也非常轻量级。

[Github Pages](https://pages.github.com/)是Github上的一项服务，注册用户可以申请一个和自己账号关联的二级域名，在上面可以托管一个静态网站，网站内容本身就是Github的一个repository也就是项目，维护这个项目的代码就是在维护自己的网站。

此外，用户撰写日志使用的是[Markdown](http://wowubuntu.com/markdown/)语法。这是一种极简化的语法，它的好处在于可以以纯文本形式表现文章，用户不用关心排版的问题。基本上来说它相当于HTML标签的最小子集做了一个转义。

综上所述，使用本文所述的配置环境，你将得到一个如下的个人网站：

- 完全免费的空间和域名
- 在本地撰写日志和管理网站，不用考虑备份问题
- 无需管理数据库
- 满足你的折腾欲

下面进入正题。

## 安装 ##
### Ruby环境的安装 ###
Octopress是基于Ruby的，我们需要安装一个Ruby环境和一个开发包。

首先我们在[这个页面下载Ruby环境](http://rubyinstaller.org/downloads/)。这里要注意一下版本，请选择和当前[Ocotopress官网的建议](http://octopress.org/docs/setup/)相同的版本。本文目前要下载的是[Ruby 1.93](http://dl.bintray.com/oneclick/rubyinstaller/rubyinstaller-1.9.3-p551.exe?direct)。安装时**注意选中**“Add Ruby executables to your PATH”的选项，将Ruby程序加入环境变量，这样才能在Windows任意位置使用cmd运行Ruby命令。

安装结束后，可以在cmd输入如下命令检测ruby是否安装成功：

    ruby --version

下面需要安装[DevKit](https://github.com/oneclick/rubyinstaller/wiki/Development-Kit)。DevKit是专在Windows上为了方便使用Ruby的一个开发包，在它主页的Requirements一段有写根据你的Ruby版本，对应的DevKits版本也不同。本文需下载[DevKit 4.5.2](https://github.com/downloads/oneclick/rubyinstaller/DevKit-tdm-32-4.5.2-20111229-1559-sfx.exe)版本。

下载解压到没有空格的路径，然后在此路径打开cmd（鼠标玩家请在文件夹上按shift+右键，选择在此处打开命令窗口），执行以下命令：

	ruby dk.rb init
	ruby dk.rb install

### Octopress的安装 ###
进入[OCtopress在Github](https://github.com/imathis/octopress)的页面，点击右边的Downloadzip。会用Github的玩家可以直接`git clone`，不多叙述。下载下来之后解压，把目录名字改成octopress。

下面我们先做个预备操作：更改gem的更新源。由于后续安装需要gem自己从服务器上拿资源，默认的服务器地址速度很慢，因此我们改成淘宝的源。

	gem sources -a http://ruby.taobao.org/
	gem sources -r http://rubygems.org/
	gem sources -l

三行命令的作用分别是：添加淘宝源；删除默认源；显示当前源列表。显示淘宝地址就表示成功。

然后，我们还要改一下Octopress的gem更新源：在octopress的目录下，打开`Gemfile`文件，查找`source "http://rubygems.org/"`改为`source "http://ruby.taobao.org/"`。

好了下面我们进入本节的最后一步：安装Octopress依赖。在Octopress的目录下执行命令：

	gem install bundler
	bundler install
	
这一步需要等待一段时间，耐心等待出现Complete以后，执行以下命令安装Octopress的默认主题：

	rake install

### 测试 ###
现在我们本地已经环境全部安装完毕了，你可以在自己的电脑上开始耍了。

由于网站是静态的，所以每次改动（修改网页，写日志等）之后需要使用以下命令生成页面：

	rake generate

生成完毕后，使用以下命令启动网站进程，默认占用4000端口：
	
	rake preview

注意这个命令运行之后当前窗口会一直以阻塞模式监听http请求和显示状态，所以要输入新命令请新开一个cmd。

之后在浏览器输入`http://localhost:4000`就可以看到网站的界面了。

## 配置 ##

### 基础配置 ###
网站的通用配置文件是根目录下的`_config.yml`，内有注释已经将配置分成几个部分。我会把我改动过的部分列出来，其余的部分略过，可以自己研究。

1) 主配置Main Configs

	url: http://blog.bandon.me	//博客地址
	title: 班登 - 博客	//博客标题
	subtitle: Code is sexy.	//副标题
	author: 班登	//作者，显示在网站最下方Copyrights处
	simple_search: www.google.com.hk/search //默认搜索引擎
	date_format: "ordinal" //日期格式

这里日期格式默认是ordinal，显示的形如`July 22nd 2007`这样的日期，如果要显示`2007年07月22日`这种，可以把`date_format`改为`%Y年%m月%d日`。这个字串内容就是一个常见的日期转string的字串，格式参考详见[ruby文档](http://www.ruby-doc.org/core-1.9.2/Time.html#method-i-strftime)。

2) Jekyll & Plugins 

这个部分我没改动。如果你发布的内容是网站的子目录（如`http://site.com/project`）下，就把`root:/`改为`root:/project`。

3) 第三方插件 3rd Party Settings

这里设置第三方插件的设定，譬如Github个人资料、Twitter、G+、Pinboard、脸书等。总之基本上要打开就改成`true`否则就`false`，要填用户名就填就可以了。

由于是纯静态网站，因此网站评论必须使用第三方插件。Octopress默认支持Disqus，在[Disqus官网](https://disqus.com)注册一个账号，进入个人面板在右上角齿轮点击[添加到站点](https://disqus.com/admin/create/)，Site name填你的网站域名，下面自动生成的字串是你的**shortname**，比如我的是`blogbandonme`。申请完毕后，在配置文件中找到以下内容修改：

	disqus_short_name: blogbandonme //改成你的shortname
	disqus_show_comment_count: true

默认嵌入的disqus插件是英文的，要显示中文版，添加到站点完毕后，进入你站点的控制面板（形如`https://blogbandonme.disqus.com`），点击右侧**Settings**，找到lauguage一栏，改成**Chinese**提交即可。

### 中国特色 ###

由于**防火长城**的原因，脸书、推、G+的服务在国内都没法用，加上Octopress引用的一些资源都来自Google，导致默认的网站加载起来非常非常慢（因为加载资源超时拖慢了整体速度）。因此我们需要对这些引用做一些改动以加快速度。

1) 修改jQuery的源

打开`source/_includes/head.html`，找到如下字串

    <script src="//ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.min.js"></script>

改为

	<script src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.9.1.min.js"></script>

2）修改字体源

Octopress的英文字体是加载的Google Fonts，我们将其改成国内的CDN源，打开`source/_includes/custom/head.html`,将其中的`https://fonts.googleapis.com`改为`http://fonts.useso.com`即可。

3） 关闭Twitter插件

在前面提到的`_config.yml`中`twitter_tweet_butto`改为`false`即可。

### 中文化 ###

Octopress的页面中很多显示信息都是英文的，我们可以改成中文。直接打开文件改对应字符串保存即可。

- 顶部导航在`source/_includes/custom/navigation.html`
- 底部版权在`source/_includes/custom/footer.html`
- 侧边栏的RecentPosts标题在`source/_includes/asides/recent_posts.html`

这里写到的只是其中的一部分，总之源码都在`source`目录，需要改动哪个部分找字串然后改了保存就可以汉化了。

## 撰写日志 ##

要发布一篇新文章，在命令行中输入以下命令：

	rake new_post["mypost"]

之后在`source/_posts`里就可以找到这个文件形如`2015-01-10-mypost.markdown`，打开编辑就可以写日志内容了。

文件里已经默认有一段基本属性的说明：

	---
	layout: post 
	title: "利用Github Pages在Windows平台部署Octopress"
	date: 2015-01-10 03:02:43 +0800
	comments: true
	categories: Octopress
	---

`title`默认和你新建文章时的名字相同，这里可以修改。`comments`表示是否允许评论。`categories`是自己分类用的目录。

之后文章正文的语法就用Markdown来写。语法参考可见[这里](http://wowubuntu.com/markdown)；如果你像我一样是个新手需要在写的时候即时预览效果，你可以使用[MarkdownPad](http://markdownpad.com/download.html)这款软件。

写完文章之后就可以用`rake generate`生成一下静态了。如果你之前已经运行了`rake preview`启动了网站，那么文章保存后自动就生成静态了，比较方便即时查看。

另外还需要注意一下，写好的文章要保存为utf-8无bom格式，否则对中文支持会有问题。

## 发布 ##
### 申请空间 ###

1. 在[Github](https://github.com/)注册一个账号。
2. 登录，创建一个新仓库（[Create new repository](https://github.com/new)），Repository name一定要写成username.github.io的形式，username是你刚注册的账号名称。其他的不用变。申请完毕后大约几分钟，域名[https://username.github.io](https://beside4ever.github.io)就可以访问了。

### 本地配置 ###
3. 在[Git官网](http://www.git-scm.com/)下载[git for windows](http://www.git-scm.com/download/win)并安装。安装完成后可在cmd输入`git version`来确认是否安装成功。
4. 在octopress的目录下输入命令`rake setup_github_pages`进行一系列自动的设置。期间会让你输入以下你的Github账号和密码。
5. 完毕以后就可以准备发布了，生成还是`rake generate`，发布请执行`rake deploy`。
6. 发布完毕后查看[https://username.github.io](https://beside4ever.github.io)就可以看到我们的网站已经运行了。
6. 发布时**可能**会提示你
`Updates were rejected because the tip of your current branch is behind its remote counterpart.`
也就是告诉你远程目录已经有内容了，此时请到发布的目录`_deploy`下，打开一个新的cmd，运行命令：`git push -f`，强制用本地内容覆盖远程即可。

### 自定域名 ###

Github Pages支持绑定其他域名，如果你有自己的域名，可以将你自己的域名解析过来。

首先，在你的`source`目录下新建一个文件`CNAME`，输入你自己域名的地址（不要带协议头），如我的：`blog.bandon.me`；

然后，到你自己域名的注册商或DNS运营商那里添加一条解析记录。

- 一级域名（example.com）请增加一条`A`型记录，指向`192.30.252.153`或`192.30.252.154`
- 二级域名（www.example.com，或我的blog.bandon.me）请增加一条`CNAME`型记录，指向你原先的Github Pages域名，比如我的是`beside4ever.github.io`。

**注意！**不要将一级域名解析成CNAME记录！

解析大概需要数分钟不等，解析成功后访问你自己的域名即等同于原先访问Github的域名。

至此，全部流程完毕。

## 结语 ##
正如octopress的标语所说的那样：

>  A blogging framework for hackers. 

这是一个生来就给人折腾的博客……说来也巧，多年前第一次开始正经写程序就是用的Ruby（RPG Maker Script），如今仿佛一个循环，又来折腾它了。

欢迎来折腾。

