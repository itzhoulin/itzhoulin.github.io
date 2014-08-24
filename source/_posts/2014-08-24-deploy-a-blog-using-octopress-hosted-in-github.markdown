---
layout: post
title: "deploy a blog using octopress hosted in github"
date: 2014-08-24 14:28:54 +0800
comments: true
categories: [octopress, github]
---

这里将就详细讲解一下如何利用octopress在github上部署一个博客程序，既方便写博客又无需考虑托管问题，同时天然的在本地有一个备份，所有操作都基于git，有无限版本控制。使用markdown书写具体的博客内容，自带代码高亮解决方案。

github使用的博客引擎是**Jekyll**，octopress也是利用Jekyll引擎来开发博客的，因此生成的博客系统可以直接在github pages上运行良好。

## 环境说明:
本地环境是在ubuntukylin14.04.1系统下部署的，其他系统也没问题，只需要支持ruby，git

本文主要参照octopress官网和破船之家的教程写的。

## 目录
1. [Ruby等依赖安装](#ruby_git_install)
2. [octopress安装](#octopress_install)
3. [octopress配置](#octopress_config)
4. [部署到github](#deploy2github)
5. [发布博客](#post_blog)
6. [Ruby等依赖安装](#ruby_git_install)

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

* 最好把里面的twitter相关的信息全部删掉，否则由于GFW的原因，将会造成页面load很慢。
* 同理，修改定制文件/source/_includes/custom/head.html 把google的自定义字体去掉。from[唐巧的博文中—配置](http://blog.devtang.com/blog/2012/02/10/setup-blog-based-on-github/)


***注意：***

*确保不使用跟其他project同名的分类名，因为这样会引起路径冲突。*


参考链接：

1. [octopress](http://octopress.org/docs/)
2. [破船之家](http://beyondvincent.com/blog/2013/08/03/108-creating-a-github-blog-using-octopress/)
