---
layout:     post
title:      Docker Tutorial for Java
subtitle:   
date:       2021-07-13
author:     Hao
header-img: img/post/post_bg_coffee.jpg
catalog: true
mathjax: true
tags:
    - Git
---

为了学习 Git，我们先从 *版本控制* 讲起。

## Version Control

版本控制是一种记录一个或若干文件内容变化，以便将来查阅特定版本修订情况的系统。在项目开发过程中，通常是由一个团队协同完成项目，所以跟踪和记录整个开发过程、协同多人并行开发、组织和保护源代码文档就变得尤为重要。

常用的项目版本控制工具有很多，目前使用最广泛的是 Git、SVN (Subversion) 这两种。

### 版本控制分类

1、本地版本控制

许多人习惯用复制整个项目目录的方式来保存不同的版本，或许还会改名加上时间戳以示区别。这么做唯一的好处就是简单，但是特别容易犯错。 有时候会混淆所在的工作目录，一不小心会写错文件或者覆盖意想外的文件。

为了解决这个问题，人们很久以前就开发了许多种本地版本控制系统，大多都是采用某种简单的数据库来记录文件的历次更新差异。

![img](/img/post/Git/local_vc.png)

2、集中版本控制

如何让多个开发者协同工作？集中化的版本控制系统应运而生。这类版本控制工具都有一个单一的集中服务器，保存所有文件的修订版本。协同工作的人员都通过客户端连接到这台服务器，拉去最新文件或者提交文件更新。

![img](/img/post/Git/center_vc.png)

集中化版本控制工具已经发展非常成熟，典型的就是 SVN。但这样的版本控制系统最显而易见的缺点就是集中服务器的单点故障问题。一旦集中服务器宕机，那么谁都无法提交更新，也无法协同工作。假设一种更坏的情况，如果集中服务器的数据库磁盘损坏，那么整个项目的变更历史都将丢失。

3、分布式版本控制

于是分布式版本控制系统面世了，典型的就是 Git。在这类版本控制工具中，客户端并不只提取最新版本的文件快照，而是把代码仓库完整地克隆下来，包括完整的历史记录。这么一来，任何一处协同工作的服务器发生故障，都可以用其他任意一个克隆出来的本地仓库恢复。因为每一次的克隆操作，都是对代码仓库的完整备份。

![img](/img/post/Git/distribute_vc.png)

## What is Git

Git 是一种免费、开源的分布式版本控制系统。最初是由 Linux 之父 Linus Benedic Torvalds 开发，用于辅助 Linux 内核开发，是目前世界上最先进的分布式版本控制系统。

## Installing Git

在 Git 官网 (https://git-scm.com/downloads)，下载对应系统的安装包。

Tip：如果官网下载很慢，可以使用淘宝镜像下载：http://npm.taobao.org/mirrors/git-for-windows/

安装过程一路 Next 即可。接下来我们要配置 Git，Git 配置文件保存在本地，我们可以通过以下命令查看。

```bash
# 查看系统config
$ git config --system --list
# 查看当前用户（global）配置
$ git config --global --list
```

我们需要设置用户名和邮箱，用于提交记录时标识自己的身份。

```bash
$ git config --global user.name <your username>
$ git config --global user.email <your email>
```

## 工作区域

Git 本地有三个工作区域：工作目录 (WorkSpace)、暂存区(Stage)、本地仓库 (Local Repo)，再加上远程仓库 (Remote Repo) 一共有四个区域。这四个区域之间的关系如下图所示。

![img](/img/post/Git/repo.png)

+ WorkSpace: 即我们的项目代码目录；
+ Stage: 临时存放文件的改动，其实它是一个文件；
+ Local Repo: 本地仓库，安全地存放版本数据；
+ Remote Repo: 远程仓库，即托管代码的服务器，如 GitHub；

## 一般流程

使用 Git 的一般流程是，

1. 在 WorkSpace 中添加和修改文件，用到 git add；

2. 添加需要版本控制的文件到暂存区，用到 git commit；

3. 将暂存区中的文件提交到 Git 仓库，用到 git push；

## Git 项目搭建

### 本地仓库搭建

第一种方式是，在本地创建一个新目录，然后使用 Git 初始化。

```
$ git init
```

当执行完上面 Git 初始化命令后，可以看到目录中出现一个 .git 隐藏目录，该目录保存所有版本信息。

### 克隆远程仓库

另一种方式是，现在远程仓库创建一个 Repo，然后将其克隆到本地。

```
$ git clone <repo url>
```

这样克隆到本地的仓库就自动包含 Git 信息。

## 忽略文件

有时候我们不想把某些文件纳入版本控制中，比如数据库文件，临时文件，设计文件等。此时，我们可以在主目录下建立 .gitignore 文件，此文件有如下规则：

1. 文件中的空行或以 # 开始的行将被忽略。

2. 可使用 Linux 通配符。例如，'*' 代表任意多个字符，'?' 代表一个字符，'[abc]' 代表可选字符范围，'{string1,string2,...}' 代表可选的字符串等。

3. '!' 表示例外规则，该文件将不被忽略。

4. 以路径分隔符 '/' 开头，表示要忽略的文件在此目录下，而子目录中的文件不忽略。

5. 以路径分隔符 '/' 结尾，表示忽略该名称的子目录。

```bash
#为注释
*.txt        # 忽略所有 .txt结尾的文件,这样的话上传就不会被选中！
!lib.txt     # 但lib.txt除外
/temp        # 仅忽略项目根目录下的TODO文件,不包括其它目录temp
build/       # 忽略build/目录下的所有文件
doc/*.txt    # 会忽略 doc/notes.txt 但不包括 doc/server/arch.txt
```

## 分支

分支在 Git 中相对较难，分支相当于科幻电影里面的平行宇宙，如果两个平行宇宙互不干扰，那对现在的你也没啥影响。不过，在某个时间点，如果两个平行宇宙合并了，我们就需要处理一些问题了！
如果同一个文件在合并分支时都被修改了则会引起冲突，解决的办法是我们可以手动修改冲突文件，确定选择保留哪一方的修改后重新提交。

### Git 分支常用命令

```bash
# 列出所有本地分支
git branch

# 列出所有远程分支
git branch -r

# 新建一个分支，但依然停留在当前分支
git branch [branch-name]

# 新建一个分支，并切换到该分支
git checkout -b [branch]

# 合并指定分支到当前分支
$ git merge [branch]

# 删除分支
$ git branch -d [branch-name]

# 删除远程分支
$ git push origin --delete [branch-name]
$ git branch -dr [remote/branch]
```

## 总结

本篇我们简单学习了 Git，主要从版本控制的由来、Git 的一般流程、分支管理方面介绍。Git 分支相对较难，在以后实际工作中多加练习应该会有更深的体会。


参考自：
1. [Git Documentation](https://git-scm.com/doc)
