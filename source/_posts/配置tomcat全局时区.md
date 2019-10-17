---
title: 前后端分离vue实践
date: 2019-10-03 02:14:44
tags:
- 服务器		
categories:
- 服务器
---

昨天技术那边反馈问题登陆报错。排查发现是时间问题。客户是沙特阿拉伯的 东三区。

默认是东八区。

我首先是给这个客户改了下代码

```
SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
		dateFormat.setTimeZone(TimeZone.getTimeZone("Etc/GMT-3"));
		或者
		
		dateFormat.setTimeZone(TimeZone.getTimeZone("GMT+3:00"));
```

替换.class问题消除;

然后细细一想 局部声明了时区，其他要用到日期转换的还是会报错

## 全局时区：

服务器用的tomcat 

打开tomcat/bin/ catalina.bat

搜索这段配置

```
if not exist "%CATALINA_BASE%\conf\logging.properties" goto noJuli
```

找到

```
if not exist "%CATALINA_BASE%\conf\logging.properties" goto noJuli
set JAVA_OPTS=%JAVA_OPTS%  -Xms1024m -Xmx1024m -Dfile.encoding=GBK  -Duser.timezone=GMT+08 -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djava.util.logging.config.file="%CATALINA_BASE%\conf\logging.properties"
:noJuli

```

将8改为3  

问题解决。