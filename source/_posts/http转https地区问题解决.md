---
title: http转https地区问题解决
date: 2019-03-31 13:10:06
tags:
- 服务器域名相关
categories:
- 服务器域名相关
---

####   前言

之前使用freessl生成证书配置apache进行http转https的操作，这次客户阿里云服务器地区为香港使用freessl就是无法生成证书，阿里云服务器那边排查发现没有错误,多方寻找找到了解决方案。

### 免费DV证书申请

[数安时代](https://certmall.trustauth.cn/Free)

[关于域名的一些知识及问题解决](https://www.trustauth.cn/baike)

[证书部署安装指南](https://www.trustauth.cn/ssl-guide)

freessl就是无法验证通过，如果国内的服务器是没问题的。在此记录。