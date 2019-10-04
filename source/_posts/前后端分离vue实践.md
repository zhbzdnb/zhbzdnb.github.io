---
title: 前后端分离vue实践
date: 2019-10-03 02:14:44
tags:
- 前端
categories:
- 前端
---

首先是前后端分离概念。很简单 前端可以独立存在，只是没有数据。后端不在负责页面跳转，变成编写各种api文档以供前端调用。

反观传统jsp JavaServerPages java服务页面，各种标签依赖于后台控制层的传值，数据耦合性高。

一直用的jsp没去接触前后端解耦

vue教程https://cn.vuejs.org/v2/guide/

简单入门没什么问题

从git上面拿一个开源项目作为参考

https://github.com/bailicangdu/vue2-manage

此项目前端 vue+ element-ui 

element-ui https://element.eleme.cn/#/zh-CN/guide/nav 

一般用vue开发大型项目都会用到webpack

参考文章https://www.jianshu.com/p/6edbdfca1370

他这里说的一段话很清楚明白了

**使用vue.js开发大型应用需要使用webpack打包工具，Webpack 是一个前端资源的打包工具，它可以将js、image、css等资源当成一个模块进行打包。**

**webpack基于node.js运行**

**传统意义上的 JavaScript 运行在浏览器上，Chrome 使用的 JavaScript 引擎是 V8，Node.js 是一个运行在服务端的框架，它的底层就使用了 V8 引擎，这样就可以使用javascript去编写一些服务端的程序，这样也就实现了用javaScript去开发 Apache + PHP 以及 Java Servlet所开发的服务端功能，这样做的好处就是前端和后端都采用javascript，即开发一份js程序即可以运行在前端也可以运行的服务端，这样比一个应用使用多种语言在开发效率上要高，不过node.js属于新兴产品，一些公司也在尝试使用node.js完成一些业务领域，node.js基于V8引擎，基于事件驱动机制，在特定领域性能出色，比如用node.js实现消息推送、状态监控等的业务功能非常合适。**

关于从github上面clone的开源vue项目也会用到webpack的一些知识。

可简单安装实践。

个人觉得越新的框架，越是简单，这是一种趋势。