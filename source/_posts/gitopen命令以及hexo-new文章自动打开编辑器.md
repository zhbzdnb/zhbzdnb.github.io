---
title: gitopen命令以及hexo-new文章自动打开编辑器
date: 2019-08-11 20:30:36
tags:
- git
- hexo优化相关
categories:
- git
- hexo优化相关
---

## git open

当你commit更改到项目后，又去打开你项目git网站查看是否成功，现在这个命令可输入直接打开。

git open项目地址https://github.com/paulirish/git-open

关于其安装项目说明里有自行查阅

## hexo new 文章后自动打开编辑器

这个文章一多，一new，去找文章打开很麻烦。

显然hexo开发团队也注意到了这点。

在你hexo博客(注意不是静态目录)下面创建一个scripts的文件夹

下面放一个任意名的js,放上这段代码

```
var spawn = require('child_process').exec;
// Hexo 2.x 用户复制这段
//hexo.on('new', function(path){
//  spawn('start  "markdown编辑器绝对路径.exe" ' + path);
//});
//D:\WorkPlace\MarkdownPad\MarkdownPad2.exe 是MakdownPad编辑器在我本地的路径！
// Hexo 3 用户复制这段
hexo.on('new', function(data){
  spawn('start  "D:\WorkPlace\MarkdownPad\MarkdownPad2.exe" ' + data.path);
});

```

new 即可查看效果。