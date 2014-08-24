---
layout: post
title: "deploy a blog using octopress hosted in github"
date: 2014-08-24 14:28:54 +0800
comments: true
categories: [octopress, github]
---

这里将详细讲解一下如何利用octopress在github上部署一个博客程序，既方便写博客又无需考虑托管问题，同时天然的在本地有一个备份，所有操作都基于git，有无限版本控制。使用markdown书写具体的博客内容，自带代码高亮解决方案。

github使用的博客引擎是**Jekyll**，octopress也是利用Jekyll引擎来开发博客的，因此生成的博客系统可以直接在github pages上运行良好。
<!-- more -->
## 环境说明:
本地环境是在ubuntukylin14.04.1系统下部署的，其他系统也没问题，只需要支持ruby，git

本文主要参照octopress官网和破船之家的教程写的。

## 目录
1. [Ruby等依赖安装](#ruby_git_install)
2. [octopress安装](#octopress_install)
3. [octopress配置](#octopress_config)
4. [部署到github](#deploy2github)
5. [发布博客](#post_blog)
6. [小结](#summary)


## 1.Ruby等依赖安装
<a id="ruby_git_install">Ruby </a>需要1.9.3版本的，同时由于Jekyll和octopress都是ruby写的，会有诸多ruby依赖，建议切换ruby源为国内源。对于git版本没有太大要求。
#### Install Ruby
查看ruby版本方法如下

    ruby --version
    
如果版本符合要求则进入下一步，否则请参照[官方手册安装ruby](https://www.ruby-lang.org/en/downloads/)或者使用[RVM](http://octopress.org/docs/setup/rvm/)来安装。

#### 安装bundler
bundle可以自动解决依赖，安装方法如下：
    
    gem install bundler
    
建议国内用户切换gem源为国内源，方法如下：

```bash
#删除官方源
gem source -r https://rubygems.org/
#添加淘宝源
gem source -a http://ruby.taobao.org/
#查看当前源
gem source -l
```

#### 安装git
如果已经安装git，执行命令返回值为具体版本，否则请自行[安装git](http://git-scm.com/)

    git --version
    #git version 1.9.1

## 2.octopress安装
<a id="octopress_install">octopress </a>的安装也比较简单，下载源码后会有Gemfile文件来指示所有依赖，使用bundle即可。

#### 下载源码
```bash
git clone git://github.com/imathis/octopress.git octopress
cd octopress
```

#### 安装octopress
使用bundle自动安装，将下面的内容写入文件Gemfile中放在站点仓库根目录下

    source "http://ruby.taobao.org"
    gem 'github-pages'
    
这样执行命令***bundle install***就会自动安装所有octopress及其所有依赖。

**注意：**
如果上面的命令执行失败，提示下面的错误：

```
Gem::Installer::ExtensionBuildError: ERROR: Failed to build gem native extension.

        /usr/bin/ruby1.9.1 extconf.rb 
/usr/lib/ruby/1.9.1/rubygems/custom_require.rb:36:in `require': cannot load such file -- mkmf (LoadError)

```

请执行下面的命令安装ruby1.9.1-dev

    sudo apt-get install ruby1.9.1-dev
    
#### 安装octopress默认主题

    rake install
    
在octopress根目录下的Rakefile定义了如何将octopress跟Jekyll连接起来，rake打包了一些常见的如发布博客主题、生成博客数据、发布博客等一系列命令来简化博主的操作。直接使用Jekyll每一个任务都需要不少命令和文件编辑。

####tips： 保持Jekyll最新
由于Jekyll更新比较频繁，为了确保你在本地调试的时候看到的网站效果跟GitHub Pages上的一致，务必时常更新Jekyll，推荐使用bundle更新，命令如下：

    bundle update
    
## 3.octopress配置
实际上octopress已经很好的隐藏了Jekyll的诸多配置，我们只需要简单操作几个文件。octopress就会自动生成相应的Jekyll配置。这里我们仅仅讲解一下_config.yml文件的部分配置项，具体请看官网[Configuring Octopress](http://octopress.org/docs/configuring/)。

该文件的配置分为三大部分:Main config、Jekyll&Plugin、3rd Party Settings。
#### Main config

```yaml
url:                # For rewriting urls for RSS, etc
title:              # Used in the header and title tags
subtitle:           # A description used in the header
author:             # Your name, for RSS, Copyright, Metadata
simple_search:      # Search engine for simple site search
description:        # A default meta description for your site
date_format:        # Format dates using Ruby's date strftime syntax
subscribe_rss:      # Url for your blog's feed, defauts to /atom.xml
subscribe_email:    # Url to subscribe by email (service required)
category_feeds:     # Enable per category RSS feeds (defaults to false in 2.1)
email:              # Email address for the RSS feed if you want it.
```

注释说明已经很详细了，有一点需要强调的是，**如果你想启用自己的域名来访问，这里的url务必修改为你自己的域名，否则即便你添加了CNAME文件，使用rake部署的时候也不会部署该CNAME文件**。

***建议：***

* 最好把里面的twitter相关的信息全部删掉，否则由于GFW的原因，将会造成页面load很慢。涉及到的文件比较多，小心删除，如果以发表博客，_deploy目录下无需手动删除，重新生成博客后会自动删除。
* 同理，修改定制文件/source/_includes/custom/head.html 把google的自定义字体去掉。from[唐巧的博文中—配置](http://blog.devtang.com/blog/2012/02/10/setup-blog-based-on-github/)

**关于另外两部分配置后面会有文详细介绍，这里略过。**

## 4.部署到github
<a id="deploy2github">github pages</a>支持托管，地址为[https://pages.github.com/](https://pages.github.com/)。在这个页面你只需要大概了解一下什么事github page，如何申请就行了。

1. 新建一个仓库，名为your_user_name.github.io或organization.github.io，当然也可以是project，这个方法有点不一样，这里以your_user_name.github.io为例。
2. 在octopress根目录执行 *rake setup_github_pages* ，按照要求输入仓库地址等，这个命令会在跟目录下新建_deploy目录，这个会push到仓库的master分支，也就是访问博客的文件。
3. 生成博客， *rake generate* 这个会按照既定规则生成静态文件的博客。
4. 发布博客， *rake deploy* 将前一步生成的bo9ke拷贝到_deploy目录并push到github

**注意：**

第一次执行 *rake deploy* 的时候会报错如下：
```bash
 ! [rejected]        master -> master (non-fast-forward)
error: failed to push some refs to 'https://github.com/yeesterbunny/yeesterbunny.github.com.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Merge the remote changes (e.g. 'git pull')
hint: before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

解决方法是手动进入_deploy目录手动强制push一次。
```bash
cd octopress/_deploy
git pull origin +master
cd ..
rake deploy
```
这样再次执行就不会报错了。
#### 博客本地预览
如果在部署到仓库之前，需要先预览一下博客，可以在终端输入 *rake preview* 命令，然后就能在浏览器中进行本地预览访问了： [http://127.0.0.1:4000/](http://127.0.0.1:4000/)或[http://localhost:4000/](http://localhost:4000/)，效果跟仓库中的一样。

#### 提交源码用于备份
前面提到的deploy只是部署博客代码到github上面，整个octopress并没有提交，为了保证在任何地方随时发布博客，而无需再次详细配置以保证跟github上的最新代码一致，可以将当前octopress的源码存放到github的source分支下：
```bash
$ git add .
$ git commit -m 'Initial source commit'
$ git push origin source
```
## 5.新建博文
<a id="post_blog">新建博文</a>也非常简单，octopress已经预定义了脚本 ***rake new_post["title"]*** 方便我们操作，因为Jekyll有一套固定的文档路径以及命名规范，需要按照它的规定来实现才能成功生成博文。博文必须存储在source/_posts目录下，并且需要按照Jekyll的命名规范对文章进行命名：YYYY-MM-DD-post-title.markdown。文章的名字会被当做url的一部分，而其中的日期用于对博文的区分和排序。

new_post执行后会自动新建博文，并在相应的markdown文件中写入yaml元数据。
```yaml
---
layout: post
title: "title"
date: 2013-08-03 16:36
comments: true
categories: 
---
```
这里的categories是自定义的分类名，支持的定义方式如下：
```yaml
# One category
categories: Sass
 
# Multiple categories example 1
categories: [CSS3, Sass, Media Queries]
 
# Multiple categories example 2
categories:
- CSS3
- Sass
- Media Queries
```
除此之外，还支持 ***author: Your Name*** 来指明作者，支持 ***published: false*** 表明当前博文是草稿暂时不发布。

#### 发布博文完整流程：
```bash
rake new_post["New Post"]
#edit the file source/_posts/{DATETIME}-New-Post.markdown
rake generate
git add .
git commit -am "Some comment here." 
git push origin source
rake deploy
```

#### 新建页面
你可以在博客源目录下任意地方添加页面，Jekyll会自动拼接，对应的url地址也会根据页面路径自动生成 ，如果要添加 ***about.markdown*** ，那么就会生成 ***site.com/about.html*** ，如果你想要的url地址是 ***site.com/about/*** ,你需要新建的页面应该是 ***about/index.markdown*** .同样的，octopress也有脚本完成这个操作。
```
rake new_page[super-awesome]
# creates /source/super-awesome/index.markdown
 
rake new_page[super-awesome/page.html]
# creates /source/super-awesome/page.html
```

像发布博文一样，默认后缀依然是markdown，但是你可以再Rakefile中修改这个默认配置；一个干净的页面文件如下：
```
---
layout: page
title: "Super Awesome"
date: 2011-07-03 5:59
comments: true
sharing: true
footer: true
---
```
这里的title来自于文件名。你也可以手动修改。跟博文的一样，除了不包含分类 categories，对于 ***sharing*** 和 ***comments***  你可以关闭，对于 ***footer***   你可以删除，这样就不会添加默认的footer信息到该页面；如果你不像要再页面中显示日期，可以删除这里的 ***date*** 。

#### 内容
页面和博文会调用markup 引擎渲染，默认引擎是再配置文件中；此外，你可以使用任何[Jekyll docs](http://jekyllrb.com/docs/variables/)中所介绍的[模板特性](https://github.com/Shopify/liquid/wiki/Liquid-for-Designers)。

默认首页是显示全文，要想仅仅显示摘要，请在合适的地方插入下面的代码

     <!-- more -->
     
这个会生成一个"Continue →" 链接来指向完整博文，这个跟wordpress的一样。

## 6.小结
<a id="ruby_git_install">本文</a>主要是介绍如何利用octopress再github上搭建 一个博客，简述了完整步骤，同时介绍了博客涉及到的最常用的发布博文以及新建页面功能。

更加高级一点的如自定义域名、评论系统、分享等后文会详述。其实就是前面省略的_config.yml配置文件的后面两大块。

## 参考链接：

1. [octopress](http://octopress.org/docs/)
2. [破船之家](http://beyondvincent.com/blog/2013/08/03/108-creating-a-github-blog-using-octopress/)
3. [第一次rake deploy报错](http://allenyee.me/blog/2013/08/21/what-i-learned-from-hosting-octopress-on-github/)
