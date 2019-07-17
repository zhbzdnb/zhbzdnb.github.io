---
title: git分支操作
date: 2019-07-14 23:01:17
tags:
- git
categories:
- git
---

#### 前言

和同学vx看到他使用**云端开发环境Cloud Studio**,我看着心痒痒，至少直接可以解决我现在关于hexo博客迁移问题。配置过程中很多git分支的操作，因为公司项目只有我一个人负责用的还是svn，所以对git只限于最基本的一些操作，在此记录git分支的一些操作.

### 创建分支

`git branch 分支名`

#### 切换分支

`git checkout 分支名`

#### 创建并切换分支

`git checkout -b <分支名>`

##### 提交到git仓库

git commit -m '注释' --注释尽量简介明了

#### 查看所有分支

 `git branch -a`

绿色为本地

前面带*号为正在使用的

红色为远程分支

#### 删除远程分支

``git push origin --delete 远程分支名`

如果报错

```
error: unable to delete 敲入的远程分支名: remote ref does not exist
error: failed to push some refs to 远程分支地址
```

大致意思是这个远程分支不存在。

##### 清一下缓存

 `git fetch -p origin`

##### 查看所有分支

 `git branch -a`

搞定。

##### 将分支推送至当前关联的库

git push origin  分支名：关联库设置的分支名

#### 删除本地分支

`git branch -d 分支名`

#### 分支合并

现分支为master

要合并develop

`git merge develop`

#### 查看分支差异

`git log 分支1...分支2`

### 关联操作库操作

##### 查看当前关联库

`git remote -v`

##### 删除关联

`git remote remove origin`

##### 建立关联

`git remote add origin 库地址`

##### 拉取指定分支

git clone -b 分支名 库地址

# git使用总结

还是有太多不熟悉，太垃圾了，以云端编写hexo博客为问题，得到解决方案：将hexo博客推送至github分支，初始化git，将hexo博客分支推送至云端开发环境 ，，但是这个hexo也要init 前面关于[hexo迁移](https://zhbzdnb.github.io/zhbzdnb.github.io/2019/06/22/hexo%E5%8D%9A%E5%AE%A2%E8%BF%81%E7%A7%BB/)的文章也说了全目录移过去是没用的，那么就是说，需要clone到云端之后，再另外一个文件夹执行hexo init， 靠这个云端没法去复制文件夹啊。蛋疼，云端编译hexo是告一段落了。但有这次深入对git命令的熟悉，对hexo博客的迁移也会变得更加简单。