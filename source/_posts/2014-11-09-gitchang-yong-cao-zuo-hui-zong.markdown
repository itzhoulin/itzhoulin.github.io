---
layout: post
title: "git常用操作汇总"
date: 2014-11-09 21:37:14 +0800
comments: true
categories: [git]
---
关于git其实用的时间还很短，两个月前因为异地多个环境需要同步开发和维护，因此试着用了git，代码使用的开源中国的私有仓库。关于github现在更多的只是阅读别人的代码学习别人的代码，还没有实实在在贡献过代码，晚上看伯乐在线推荐了一篇关于git的文章，原文连接是[我的git笔记](http://yanhaijing.com/git/2014/11/01/my-git-note/),先根据这个再加上我前段时间已经实实在在用上了的做一些我的补充说明。感谢原作者。
<!-- more -->
# 推荐两本书：
作者是 北京群英汇信息技术有限公司 高级顾问 蒋鑫

[Git权威指南](http://www.worldhello.net/gotgit/)

[GotGitHub](http://www.worldhello.net/gotgithub/)

# 配置

首先是配置帐号信息

    git config --global user.name yanhaijing
    git config --global user.email yanhaijing@yeah.net
    
    git config --list    #查看配置的信息
 
    git help config    #获取帮助信息
    
#### 配置自动换行（自动转换坑太大）

    git config --global core.autocrlf input    #提交到git是自动将换行符转换为lf
    
#### 配置密钥

    ssh-keygen -t rsa -C yanhaijing@yeah.net     #生成密钥
 
    ssh -T git@github.com     #测试是否成功
    
# 新建仓库

    git init                                    #初始化
    git status                                  #获取状态
    #上面的命令可以查看当前所处的分支，当前工作区变更记录等，很常用
    git add file                                #.或*代表全部添加
    git commit -m "message"                     #此处注意乱码
    git remote add origin git@github.com:yanhaijing/test.git   #添加源
    git push -u origin master                   #push同时设置默认跟踪分支

#从现有仓库克隆

    git clone git://github.com/yanhaijing/data.js.git   
    git clone git://github.com/schacon/grit.git mypro     #克隆到自定义文件夹
    
# 本地

    git add *                 #跟踪所有变更文件：编辑和新增

    rm *&git rm *             #移除文件
    git rm -f *               #移除文件
    git rm --cached *         #取消跟踪
    git mv file_from file_to  #重命名跟踪文件
     
    git log                   #查看提交记录
     
    git commit                #提交更新
    git commit -m 'message'
    git commit -a             #把所有已经跟踪过的文件暂存并提交
    ### 上述命令的一个关键是已经跟踪过的文件，而对于新增文件是不会将其添加跟踪并提交的
    git commit --amend        #修改最后一次提交的message信息
     
    git reset HEAD *          #取消已经暂存(git add)的文件
     
    git checkout -- file      #取消对文件的修改（从暂存区去除file）
    git checkout branch|tag|commit -- file_name   #从仓库取出file覆盖当前分支
    git checkout -- .         #从暂存区去除文件覆盖工作区
    

























# 分支

    git branch                  #列出本地分支
    git branch -r               #列出远端分支
    git branch -a               #列出所有分支
    git branch -v               #查看各个分支最后一个提交对象的信息
    git branch --merge          #查看已经合并到当前分支的分支
    git branch --no-merge       #查看为合并到当前分支的分支
     
    git branch test             #新建test分支
    git checkout test           #切换到test分支
    git checkout -b test        #新建+切换到test分支
    git checkout -b test dev    #基于dev新建test分支，并切换
     
    git branch -d test          #删除test分支
    git branch -D test          #强制删除test分支
     
    git merge test              #将test分支合并到当前分支
    git rebase master           #将master分之上超前的提交，变基到当前分支
    
# 远端

    git fetch origin                               #更新本地数据，拉取远端所有分支
    git fetch originname branchname                #拉取远端上指定分支
    git fetch originname branchname:temp           #拉取远端分支到本地分支temp
    ## 这个是我目前协同主要用到的，我会先将远端同事的分支先拉去到本地临时分支
    ##temp，再将该temp与我自己的分支合并。
    
    git merge originname branchname                #合并远端上指定分支
    git push originname branchname                 #推送到远端上指定分支
    git push originname localbranch:serverbranch   #推送到远端上指定分支
     
    git checkout -b test origin/dev                #基于远端dev新建test分支
     
    git push origin :server                        #删除远端分支
    
# 源

git是一个分布式代码管理工具，所以可以支持多个仓库，在git里，服务器上的仓库在本地称之为remote。

个人开发时，多源用的可能不多，但多源其实非常有用。

    git remote add origin1 git@github.com:yanhaijing/data.js.git
     
    git remote                            #显示全部源
    git remote -v                         #显示全部源+详细信息
     
    git remote rename origin1 origin2     #重命名
     
    git remote rm origin1                 #删除
     
    git remote show origin1               #查看指定源的全部信息

# 标签

当开发到一定阶段时，给程序打标签是非常棒的功能。

    git tag                                      #列出现有标签  
    git tag v0.1                                 #新建标签
    git tag -a v0.1 -m 'my version 1.4'          #新建带注释标签
     
    git checkout tagname                         #切换到标签
     
    git push origin v1.5                         #推送分支到源上
    git push origin --tags                       #一次性推送所有分支
     
    git tag -d v0.1                              #删除标签
    git push origin :refs/tags/v0.1              #删除远程标签
    
# 比较diff
在没有使用github等web界面来查看某一次提交的代码级别的变更记录时，使用git diff就相当有作用。
#### 查看当前版本变化情况

    git status
    位于分支 master
    您的分支与上游分支 'origin/master' 一致。
    
    尚未暂存以备提交的变更：
      （使用 "git add <file>..." 更新要提交的内容）
      （使用 "git checkout -- <file>..." 丢弃工作区的改动）
    
    	修改:         soft-install/calamari-client-build.md
    
    未跟踪的文件:
      （使用 "git add <file>..." 以包含要提交的内容）
    
    	git-guide/
    	zmanda_guides/
    
    修改尚未加入提交（使用 "git add" 和/或 "git commit -a"）

#### 查看已跟踪文件修改

```
zhoulin@gcizhoulin:~/Documents/guides-markdown$ git diff README.md
diff --git a/README.md b/README.md
index ae4e84e..2e76687 100644
--- a/README.md
+++ b/README.md
@@ -28,4 +28,7 @@ The main folders are:
 
 * 直接tab后接代码就能够以代码方式显示，**要求：**代码前后各有一个回车
 * 前后使用**```**来分割，前后如*至少*有一处有空行则类似pre显示代码；如果是前有
-* 如果是在**列表项**里面添加代码，请务必用**```**来区别代码.
\ No newline at end of file
+* 如果是在**列表项**里面添加代码，请务必用**```**来区别代码.
+
+### 2.标题显示
+要显示标题样式，除了使用#来标记外，还需要再#标记和文字之间加空格
\ No newline at end of file
```

#### 对比两次版本的差异
    
    #首先获取两版本的版本号
    git log
    #若是查看最近几次的记录后接- 加上数字，如
    git log -2
    #输出如下:
    zhoulin@gcizhoulin:~/gitlab/XwareDesktop$ git log -2
    commit ffc3f8677c98fb2d4e33eeb522eed48d42cf72cd
    Author: xinkai <xinkai.chen@qq.com>
    Date:   Sat Aug 2 13:15:40 2014 +0800

    #89: decodePrivateLink: when utf-8 decoding fails, try gb18030 decoding

    commit c80c87c358a26d5eaaca81e6bc82a87203ef2337
    Author: xinkai <xinkai.chen@qq.com>
    Date:   Fri Aug 1 20:31:50 2014 +0800

    #88: Ubuntu packaging: install python3-tk as a fallback to Qt.
    
    #要对比上面两次版本的区别命令如下:
    zhoulin@gcizhoulin:~/gitlab/XwareDesktop$ git diff ffc3f8677c98fb2d4e33eeb522eed48d42cf72cd c80c87c358a26d5eaaca81e6bc82a87203ef2337
    diff --git a/src/frontend/utils/misc.py b/src/frontend/utils/misc.py
    index 3293bb3..23d77f6 100644
    --- a/src/frontend/utils/misc.py
    +++ b/src/frontend/utils/misc.py
    @@ -26,12 +26,7 @@ def decodePrivateLink(link):
     assert len(path) == 1, "Invalid private link {}.".format(link)
 
     path = path[0].encode("utf-8")
    -
    -    toDecode = base64.urlsafe_b64decode(path)
    -    try:
    -        decoded = toDecode.decode("utf-8")
    -    except UnicodeDecodeError:
    -        decoded = toDecode.decode("gb18030")
    +    decoded = base64.urlsafe_b64decode(path).decode("utf-8")
 
     scheme = scheme.lower()
     if scheme == "thunder":

#### 查看某一次版本详细信息（含与上一次对比）

    git show
    #输出为:
    commit ffc3f8677c98fb2d4e33eeb522eed48d42cf72cd
    Author: xinkai <xinkai.chen@qq.com>
    Date:   Sat Aug 2 13:15:40 2014 +0800

    #89: decodePrivateLink: when utf-8 decoding fails, try gb18030 decoding

    diff --git a/src/frontend/utils/misc.py b/src/frontend/utils/misc.py
    index 23d77f6..3293bb3 100644
    --- a/src/frontend/utils/misc.py
    +++ b/src/frontend/utils/misc.py
    @@ -26,7 +26,12 @@ def decodePrivateLink(link):
         assert len(path) == 1, "Invalid private link {}.".format(link)
     
         path = path[0].encode("utf-8")
    -    decoded = base64.urlsafe_b64decode(path).decode("utf-8")
    +
    +    toDecode = base64.urlsafe_b64decode(path)
    +    try:
    +        decoded = toDecode.decode("utf-8")
    +    except UnicodeDecodeError:
    +        decoded = toDecode.decode("gb18030")
 
     scheme = scheme.lower()
     if scheme == "thunder":

后面可以跟具体的**版本号**     

#### 查看某一个文件的变更记录

    zhoulin@gcizhoulin:~/gitlab/XwareDesktop$ git log --pretty=oneline src/frontend/utils/misc.py
    #输出如下：
    ffc3f8677c98fb2d4e33eeb522eed48d42cf72cd #89: decodePrivateLink: when utf-8 decoding fails, try gb18030 decoding
    b8974b512f9b43336fe2bc86f27143f6b2cfd4f5 trivial: pep8 fix
    97e400716bd7b3f04f0d601ceef00a46ff5c8725 taskClassFilter use ENUMs instead of magic numbers in QML
    bc2c822a33dc9ecd72d0ff7ec0b3c3884d3995ea split utils.misc to utils.system
    0cfc211af54f0a4271e22ff87eeb9c0bea04bbb0 implement simplecache
    4a42d59560d8580e41c1807597c9fb5c9b985482 place misc under utils

# 参考资料

 - [Git参考手册](http://gitref.org/zh/index.html)
 - [Git简明手册](http://www.mceiba.com/tool/git-cheat-sheet.html)
 - [Git Magic](http://www-cs-students.stanford.edu/~blynn/gitmagic/intl/zh_cn/)
 - [Git Community Book 中文版](http://gitbook.liuhui998.com/index.html)
 - [Pro Git](http://git-scm.com/book/en/v2)
 - [图解Git](http://marklodato.github.io/visual-git-guide/index-zh-cn.html)
 - [git-简明指南](http://rogerdudler.github.io/git-guide/index.zh.html)
 - [learnGitBranching](http://pcottle.github.io/learnGitBranching/)