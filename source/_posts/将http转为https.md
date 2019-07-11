---
title: 将http转为https
date: 2019-03-24 19:54:14
tags:
- 服务器域名相关
categories:
- 服务器域名相关
---

前言:http和https的区别最大最直观的就是访问网页安全和不安全的字样，这次因为卖国外的很在意这个。

### 配置过程

以tomcat作为服务器，在tomcat里配置证书.

[这是个使用阿里云证书的配置案例](https://blog.csdn.net/zhangxing52077/article/details/72827770)

[freessl](https://freessl.cn/)

freessl必须是要完整域名，加上端口号的无法申请,域名默认访问的是80端口，所以清掉80端口的其他使用，给tomcat进行使用.