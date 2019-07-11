---
title: python爬虫实践以及理解
date: 2019-05-03 14:51:51
tags:
- python
categories:
- python
---

## 第一个问题Python爬虫是什么？有什么用？

网络爬虫（又被称为网页蜘蛛，网络机器人，在FOAF社区中间，更经常的称为网页追逐者），是一种按照一定的规则，自动地抓取万维网信息的程序或者脚本。另外一些不常使用的名字还有蚂蚁、自动索引、模拟程序或者蠕虫。

这上面整的花里胡哨的，说白了就是获取数据，从web页面获取。

## 实现一个简单的爬虫

```
import requests
res=requests.get("http://www.baidu.com")
res.encoding='utf-8'
print(res.text)
```

执行前先安装requests这个库[教程](https://jingyan.baidu.com/article/11c17a2c06b013f446e39dba.html)

直接在安装的pyhon下的python.exe执行就ok。

执行结果就是百度的html。

这里提供一下requests的[教程](https://2.python-requests.org//zh_CN/latest/user/quickstart.html)

总结：就现在学到毛皮而言，思路还是很清晰，慢慢来，慢慢拓展。

[python如何学习](https://www.zhihu.com/question/304686270/answer/554857097)