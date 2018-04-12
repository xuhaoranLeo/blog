---
title: 基于Hexo的博客搭建 for Mac
date: 2017-11-27 16:42:22
top: true
tags:
	- Blog
	- Hexo
---



*这是迁移博客后的第二篇，其实我很懒，并不喜欢写博客这种吃力不讨好的活儿，看的人也未必多，但是每次写完一篇博客之后的~~短暂的~~成就感以及写的这个过程中这种强制性的学习过程是很令人受用的。*

*既然觉得在这个博客写下去，那么就总要找点内容，这次就来写一下这个小博客的搭建过程吧，其中绝大多数都是参照的网上资料，写这篇就当做是整理归纳成自己的东西吧。*

<!-- more -->

## 关于Hexo

我先后用过博客园、简书、`GithubPage`+`Hexo`三个博客，从一个迁移到另一个根本原因就是好不好看，Hexo提供了众多丰富的主题，这点是最打动我的。至于什么是`Hexo`？套用官网的话来说：

> Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。

`Hexo`没有编辑器，也没有可视化的操作页面，在配置好必要的环境之后，书写文章要依赖其他的`Markdown`编辑器，而且关键是相对于简书这种平台类博客，缺少了推广的重要渠道，写博客自然是想让更多的人们看到，而自建博客~~（如我的博客）~~需要你去研究SEO…如果这么多缺点还没打消你看下去的想法，那么恭喜你将会有一个有（neng）个（zhuang）性（bi）的博客。

## 环境配置及安装

1. Node.js

     移步[Node.js官网](https://nodejs.org/en/)进行安装。

2. Git

     Xcode自带Git环境，或者移步[Git官网](https://git-scm.com)进行安装，再或者使用`Homebrew`安装：

     ```
     $ brew install git
     ```


---

当安装完`Node.js`和`Git`之后，就可以进行`Hexo`安装了：

```
$ sudo npm install -g hexo-cli
```

安装成功之后可以`cd`到你想要存放博客的目录，默认为`~/`目录，执行建站命令：

```
$ hexo init BlogName
$ npm install
// 成功之后的目录结构如下
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```

命令执行结束后，就可以正式使用`Hexo`了，使用`debug`模式进行查看：

```
$ hexo s --debug
```

访问`http://localhost:4000`就可以看到`Hexo`的默认界面了。



## 托管博客

在本地`Hexo`搭建好之后就要选择服务器进行托管，这里只讲一下通过`Github`进行托管的方法。关于自有VPS的托管可以参考[这篇文章](http://www.swiftyper.com/2016/04/17/deploy-hexo-with-git-hook/)。

### 创建仓库

进入`Github`新建仓库，名称格式为`UserName.github.io`，例如`xuhaoranLeo.github.io`。然后将本地`Hexo`的`站点配置文件`的配置仓库修改为当前仓库的`git`地址：

```
$ cd ~/blog/
$ open _config.yml
// 修改repository
deploy:
  type: git
  repository: https://github.com/xuhaoranLeo/xuhaoranleo.github.com.git
branch: master
```

此处注意`:`后面要接`Space`，这种格式才能被`Hexo`识别。

### 配置ssh key

仓库配置好之后，要为你的`Github`添加`ssh public key`。

> SSH 密钥对可以让您方便的登录到 SSH 服务器，而无需输入密码。由于您无需发送您的密码到网络中，SSH 密钥对被认为是更加安全的方式。
>
> SSH 密钥对总是成双出现的，一把公钥，一把私钥。公钥可以自由的放在您所需要连接的 SSH 服务器上，而私钥必须稳妥的保管好。
>
> 所谓"公钥登录"，原理很简单，就是用户将自己的公钥储存在远程主机上。登录的时候，远程主机会向用户发送一段随机字符串，用户用自己的私钥加密后，再发回来。远程主机用事先储存的公钥进行解密，如果成功，就证明用户是可信的，直接允许登录 shell，不再要求密码。这样子，我们即可保证了整个登录过程的安全，也不会受到中间人攻击。
>
> —— archlinux page

理解完`ssh key`之后就可以进行生成了：

```
$ ssh-keygen -t rsa -C "your_email@example.com"
Enter file in which to save the key (/Users/xuhaoran/.ssh/id_rsa): // 输入绝对地址和名称，直接enter使用默认
Enter passphrase (empty for no passphrase):  // 输入ssh key的密码，直接enter是不使用密码

// 生成成功
The key's randomart image is:
+---[RSA 2048]----+
|    ..o .    ..+ |
|     oo. .    o.o|
|     +  o .  o +B|
|      o+ +. + =.%|
|       +S.++ + B+|
|      ..+O= E   *|
|         +.o   .o|
|        .      . |
|                 |
+----[SHA256]-----+
```

成功生成之后使用任意方法打开`.public`文件，复制里面的密钥，粘贴到`Github -> Settings -> SSH and GPG keys -> New SSH key ->Key`中，`Title`可以随意写。



## 基本使用

安装完`Hexo`就可以进行日常使用了，在`blog`目录下新建文章：

```
$ hexo new "This is a new article"
```

`Hexo`会自动生成`.md`文件在`~/BlogName/source/_posts`目录下，可以使用各种`Markdown`编辑器进行博客的书写了。

在每篇文章里面可以配置`Front-matter`，来标记类别、时间、标签等：

```
---
title: Terminal命令小结
date: 2017-11-22 13:56:34
tags: 
	- Terminal Command Line
---
```

书写完成之后生成`generate`静态文件后进行部署`deploy`操作，会让你的文章发到`Github`托管的服务器上：

```
$ hexo g -d
```

日常书写之外，如果你还想定制多样化的博客样式，可以使用各种`Theme`，`Hexo`默认的样式为`landscape`，我的博客的样式为`NexT`。其他各种样式可以去[Hexo官网主题搜索](https://hexo.io/themes/)。

例如找到`NexT`主题后进入其[Github](https://github.com/iissnan/hexo-theme-next)，使用`git`进行安装：

```
$ git clone https://github.com/iissnan/hexo-theme-next themes/next
```

之后修改`站点配置文件`中的`theme`属性：

```
theme: next
```

最后清空缓存并重新生成部署：

```
$ hexo clean
$ hexo g -d
```

## 配置文件

在了解`Hexo`基本的部署和使用方法之后，就要进行一些站点和主题的配置了，这会让你的博客更具个人色彩。

### 站点配置文件

`blog`根目录下的`_config.yml`文件就是`站点配置文件`，可以使用编辑器打开：

```
title: Leo
subtitle: 侠心交友，素心做人。
description: 站在更高的地方，看见更广的世界。
author: 许昊然
language: zh-Hans
```

关于`title`、`description`等属性的具体表现形式是根据你使用的`Theme`而言的，`language`属性匹配的是`./themes/CurrentTheme/languages`下面的文件名，`NexT`下对应的简体中文就是`zh-Hans`，里面的内容之后再讲。

如果你的博客是在一个子目录下，可以设置`root`属性：

```
url: http://xuhaoranleo.top
root: /myBlog
```

其他的一些属性都有相关注释解释。

### 主题配置文件

在`./themes/CurrentTheme`目录下的`_config.yml`文件即是`主题配置文件`，每个主题都有自己对应的属性来配置，这里只拿`NexT`这个主题简单来说：

```
override: false // 可以用来覆盖掉站点配置文件
menu:
  home: / || home
  jottings: /tags/随笔 || calendar
  tags: /tags/ || tags
  archives: /archives/ || archive
menu_icons:
  enable: true
```

`menu`是主要需要配置的，这个就是你的博客展现出来的分类，`/`表示根目录，`||`后面接的是`icon name`，如我的博客有一个随笔分类，我是利用标签`tags`来配置的，`/tags/随笔`表示了点击的链接`随笔`标签。在随笔文章中的`Front-matter`里面配置：

```
tags: 
- 随笔
```

而具体是使用`tags`还是其他分类`archives`、`categories`，再或者是其他自定义的类别，主要还是根据每个类别的`layout`决定的，这个布局文件在`./themes/CurrentTheme/layout`，例如`tag.swig`。

```
social:
  GitHub: https://github.com/xuhaoranLeo || github
  # E-Mail: mailto:liang_andy@163.com || envelope
  微博: https://weibo.com/5711479827 || weibo
links:
  我的简书: http://www.jianshu.com/u/8f0c4d84e87c
```

`social`用来配置关于你的一些其他链接的，同样`||`后面是`icon name` ；`links`则是一些友情链接，除了关联一些你喜欢的网址，据说可以在SEO中发挥一些作用，这点我并未深入了解。

```
reward_comment: 请我喝一杯咖啡？
wechatpay: /assets/blogImg/wechatpay.jpg
# alipay: /assets/blogImg/alipay.jpg
#bitcoin: /images/bitcoin.png
```

`reward`属性是用来接受赞助的，这没什么好讲的，主要是利用这块说一下`Hexo`里面图片的摆放，正常来说`Hexo`的博客资源都是基于`source`这个目录作为根目录的，你可以把图片全部摆在一个目录中，例如`assets`，也可以在其下具体建立新的目录进行分类，如用的时候直接用`Markdown`导入就好：

```
![this is a image](/assets/blogImg/avatar.jpg)
```

其他的关于百度分析等第三方服务的接入可以自行参考[NexT文档](http://theme-next.iissnan.com/getting-started.html)来学习。

### 语言配置文件

`语言配置文件`主要是用来显示博客整体的文字效果的，目录为`./themes/CurrentTheme/languages`，例如在`主题配置文件`中我添加了`jottings`这个新的menu，那么我就要在`语言配置文件`，对应其展现的文字：

```
menu:
  home: 首页
  archives: 归档
  tags: 标签
  about: 关于
  jottings: 随笔
```

一些诸如`reward`你是想叫做`打赏`还是`乞讨`都可以在此处设置。

### 其他配置

#### Hexo置顶问题

参考[这篇文章](http://www.netcan666.com/2015/11/22/解决Hexo置顶问题/)，安装插件：

```
$ sudo npm uninstall hexo-generator-index --save
$ npm install hexo-generator-index-pin-top --save
```

之后再需要置顶文章的`Front-matter`中加上`top: true`属性即可，同时添加多个置顶按照时间顺序排序。

#### NexT自定义首页展示文章内容

之前遇到的一个需求，例如我的博客中存在`随笔`分类，而我希望我的首页只展现我的技术类文章。[这篇文章](http://forwardkth.github.io/2016/05/08/next-theme-post-visibility/)解决了这个问题，具体来说就是修改`layout`文件：

```
$ cd YourBlogDir/themes/YourCurrentTheme/layout
$ open index.swig
```

```
{% extends '_layout.swig' %}
{% import '_macro/post.swig' as post_template %}
{% import '_macro/sidebar.swig' as sidebar_template %}

{% block title %}{{ config.title }}{% if theme.index_with_subtitle and config.subtitle %} - {{config.subtitle }}{% endif %}{% endblock %}

{% block page_class %}
  {% if is_home() %}page-home{% endif -%}
{% endblock %}

{% block content %}
  <section id="posts" class="posts-expand">
    {% for post in page.posts %}
      {% if post.visible !== 'hide' %}
        {{ post_template.render(post, true) }}
      {% endif %}
    {% endfor %}
  </section>

  {% include '_partials/pagination.swig' %}
{% endblock %}

{% block sidebar %}
  {{ sidebar_template.render(false) }}
{% endblock %}
```

直接复制即可，主要就是添加了一个判断：如果文章中的`Front-matter`包含一个`visible`属性切属性为`hide`，那么首页就不进行展示。之后只要在`随笔`类的文章中添加属性即可：

```
---
title: Hello World
visible: hide
tags: 
- 随笔
---
```

## 更换个人域名

如果想进一步~~装逼~~优化，那么可以为你的`github.io`包一个更具个性的域名，域名可以在[阿里万网](https://wanwang.aliyun.com)上买，经常有活动，便宜的一年也就10多块钱。

当你选好了一个域名之后需要对其进行解析，添加解析记录：

```
记录类型: A // 指向一个IPV4地址
主机记录: @ + www // @表空，再配置一个www的，不管你是输入www.xuhaoran.top还是xuhaoran.top都可以直接进入博客
解析线路: 默认 
记录值: 添加你的github.io的IP，可以终端ping一下就知道了，例如我的是151.101.1.147
TTL值: 默认10分钟
```

配置好域名解析之后进入`Github`的仓库，`Setting` —> `Options` —> `GitHub Pages` —> `Custom domain`中设置好你申请的新域名。

![custom domain](/assets/ArticleImg/custom domain.png)

最后新建一个`CNAME`文件：

```
$ cd YourBlogDir/themes/YourCurrentTheme/source
$ touch CNAME
$ vim CNAME
```

填写你的新域名在其中，`:wq`保存离开，这样就可以让你每次编译之后都可以直接使用新域名了。请愉快地访问你的新博客地址吧。

## 备份博客

备份博客思路参考[这篇文章](https://wmaqingbo.github.io/blog/2017/07/05/备份Hexo源文件到Github/)。这里按照我的理解说一下：

![github hexo](/assets/ArticleImg/hexo-backup.png)

总体来说就是使用`git branch`来代替`静态页面仓库+源码仓库`的组合，新建一个分支并设为主分支，用来保存源码，`master`分支依然用来保存静态页面，这样做的好处是你日常的`hexo g -d`命令不受到影响，而当你面临着迁移博客的时候直接拉取你的`git`就可以了，任何修改直接提交，非常方便且简洁。

按照上面新建博客的步骤，我们已经有了一个可以部署到`Github`的博客了，现在直接对这个博客进行备份，直接简单粗暴的方法：

- 在`Github`新建仓库，名字随意
- `clone`新仓库到本地，新建分支`hexo`并切换到该分支
- 在`Github`上设置`hexo`分支为主分支
- 复制本地blog文件到本地仓库，并提交代码到`hexo`分支
- 删除`Github`上的`xuhaoran.github.io`仓库，并将新仓库`Rename`为`xuhaoran.github.io`

这样其他配置不需要修改，强行把仓库迁移了~~[手动微笑]~~，以后如果切换电脑就可以直接拉取代码了~

*这是这个博客的第二篇文章，中间断断续续地写了半个月，实在是汗颜，这两篇都是基础类的，之后会写一些iOS技术类博客，希望能给你帮助。如若有任何纰漏可以直接发邮件给我，还请见谅。*