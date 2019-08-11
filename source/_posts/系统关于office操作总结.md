---
title: 系统关于office操作总结
date: 2019-08-11 17:20:45
tags:
- 项目总结
categories:
- 项目总结
---

# 问题:

出错环境 wpsoffice+jacob+win10

出错逻辑 用户上传exel->转换为pdf

有些用户在上传ppt时候报错，经过排查只出现在win10最新的一些版本.

但有些客户的win10最新又没问题。

并且不是出在上传，而是出在上传后生成一个可供预览的图片时出现问题.

排查代码都是word、ppt、exle->pdf->img这样转换的。

ppt->pdf时出错。使用的是java的jacob组件，不断排查还原环境发现，win最新版jacob在其实列重要操作类时出错

```
ActiveXComponent  app = new ActiveXComponent("PowerPoint.Application");//实列操做ppt的
ActiveXComponent  app = new ActiveXComponent("Word.Application");//word
ActiveXComponent  app = new ActiveXComponent("Exel.Application");//exel
```

经过了解这句代码是从注册表上获得的实列。

#### 解决：

使用一台纯净环境的电脑装win10 装公司项目，找到原因

将wpsoffice换成了microsoftoffice(微软office')，问题消失.

有些客户可以是因为电脑内置了微软office注册表信息没问题，代码自然没问题。

wpsoffice最新版我也尝试过，没有作用。

而且这此解决不是真正的解决，我是报错后给出提示:上传错误！如是win10用户请点击下载微软office[服务器的一个下载外链]

其他的组件都试过了,都或多或少有问题.

#### Aspose 

转换速度慢,不破解有水印

#### poi

不说这个狗血的杂七杂八的jar包问题，还出现了exel转换pdf改了格式

#### openoffice 

我之前弄得时候 因为要下载安装就抛弃了，现在另一个同事另外一个系统也要这个，他也就弄了绿色版的openoffice内置进项目，出现格式异常的问题



总结这方面的问题:系统版本、付费、jar包复杂、格式error