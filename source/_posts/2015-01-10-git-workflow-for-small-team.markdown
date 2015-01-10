---
layout: post
title: "git workflow for small team"
date: 2015-01-10 22:21:07 +0800
comments: true
categories: [git, worktile]
---
本文主要是讲述我目前所在小团队的一种git工作流，我们团队对于git都是新手，很新很新的新手，这里跟大家分享一下我们的流程和相关操作命令，也不确定这种是否真是适合我们的工作流方式，期待高手指点。

*PS:对于git常用操作流程和一些概念见博文[Git常用操作汇总](/blog/2014/11/09/gitchang-yong-cao-zuo-hui-zong/)*
<!-- more -->
git太博大精深了，不同的团队规模、不同时期或者其他因素都可能会有不同的最适宜的工作流。目前我们团队固定开发人员4人，不定期支撑1人（高手），没有专职的测试QA等，主要是针对已有产品的二次开发，因此更多的是部分功能上的改造和一些bug的修复，因此基本是发现了、有需求了、谁手头比较空余就来负责这个任务。

下图是一个简单的流程图，这里面主要是两种人物角色，A是开发人员，B是除A外的其他开发人员，这里兼职测试（后文标题以**人员.操作步骤**）。本文以gitlab里面的库[http://git.itzhoulin.com/zhoulin/test.git](http://git.itzhoulin.com/zhoulin/test.git)为例，远程库除了master分之外还有develop分支，***master存放随时可以发布的版本，develop存放经测试通过的任务***。
{% img left /images/git-workflow.png  git-workflow  %}

## A.1克隆代码到本地
如果A本地还没有远程库的代码，执行下述clone操作获取远程库所有代码信息：

    git clone http://git.itzhoulin.com/zhoulin/test.git
    
如果A本地已经有代码，则只需**更新所有代码到最新版**：
    
    #更新远程库所有信息到本地
    git fetch origin
    
## A.2主分支基础上新建功能分支branchA
develop作为开发的主分支，所有开发都需要基于该分支的最新代码进行开发，后续开发完成也合并到该分支。因此首先以远程库中的develop的最新代码为蓝本新建本地branchA分支。

*PS:关于branchA的命名我们比较倾向于以成员名加日期或者所开发的功能点。*

    #基于远程develop库新建branchA分支，并切换
    git checkout -b branchA origin/develop
    
## A.4进行开发并提交到branchA
A用户完成开发任务后提交到本地branchA分支，**强烈建议不要等到所有开发完成后再提交，而是每一次提交都是针对一个不可分割的点，多几次提交以便于保留更多状态**。
    
    #添加修改的文件
    git add .
    #提交,会调用默认文本处理程序，请在其中详细注明所做更改以及本次修改是否需要额外的部署操作
    git commit
    
等到所有开发完成后，并将该分支推送到远程仓库，方便测试人员从远程仓库拉取到本地进行测试。

    git  push -u origin branchA
    
这样操作之后，所有用户都能看到分支branchA，通知B用户进行测试。

## B.1从远程拉取测试分支
B用户需要先从远程库拉取需要测试的分支branchA到本地分支branchAtest：

    git fetch origin branchA:branchAtest
    
测试之后如果发现不通过，则将相关信息反馈给A用户进行重新修复开发；

## B.4合并branchA
如果通过，就可以考虑将该分支合并到主库develop，同时删除该分支。

    #切换到develop分支（假设期间develop分支没有变更）
    git checkout develop
    #合并本次开发的代码到develop
    git merge branchAtest
    #如果没有冲突直接推送到远程
    git push -u origin develop

**注意:**
如果在完成测试前develop分支已经发生变更，需要先更新develop分支之后再执行合并操作，合并过程中可能会有冲突发生，需要进行手动冲突解决，然后也需要进行一定范围测试保证本次更新不会跟其他更新有冲突。

#### 如果有冲突，手动解决后重新测试提交
    #冲突的文件需要重新添加到暂存区，不冲突的会自动加入到暂存区
    git add conflictFile1
    git commit
    git push -u origin develop
    
#### 如果期间develop有过更新
    #需要先更新本地develop代码
    git checkout develop
    git pull
    #再合并代码
    git merge branchA
    #如遇冲突解决方法同上。

    

