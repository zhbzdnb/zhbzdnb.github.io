---
title: Nginx
tags:
- 服务器
categories:
- 服务器
---
## Nginx

#### 进入正题前先进行思考Nginx是什么？用来干什么的

Nginx是lgor Sysoev为俄罗斯访问量第二的rambler.ru站点设计开发的。从2004年发布至今，凭借开源的力量，已经接近成熟与完善。

Nginx功能丰富，可作为HTTP服务器，也可作为反向代理服务器，邮件服务器。支持FastCGI、SSL、Virtual Host、URL Rewrite、Gzip等功能。并且支持很多第三方的模块扩展。

Nginx的稳定性、功能集、示例配置文件和低系统资源的消耗让他后来居上，在全球活跃的网站中有12.18%的使用比率，大约为2220万个网站。

类似apache的服务器。

#### 常用功能

1、Http代理，反向代理：作为web服务器最常用的功能之一，尤其是反向代理。

所谓正向代理，最常接触的就是为了访问国外网站、谷歌啥的、进行科学上网(翻墙),这个步骤你发出请求到代理服务器，代理服务器访问你的请求、将数据返回给你.

2、负载均衡

Nginx提供的负载均衡策略有2种：内置策略和扩展策略。内置策略为轮询，加权轮询，Ip hash。扩展策略.

负载均衡。不同客户端的请求发送到不同的服务器进行处理，大量减少服务器负担。

3、web缓存

Nginx可以对不同的文件做不同的缓存处理，配置灵活，并且支持FastCGI_Cache，主要用于对FastCGI的动态程序进行缓存。配合着第三方的ngx_cache_purge，对制定的URL缓存内容可以的进行增删管理。

#### 配置文件配置

ps:公司直接用到ngnix用于替换apache的下载功能

放下我公司nginx的配置文件，重要的我以标注解释

特别注意 nginx这个配置文件nginx.conf 每个配置都必须打分号

```
worker_processes  1;  #允许生成的进程数，默认为1


events {
    worker_connections  1024;  #最大连接数，默认为512
}

http {
    include       mime.types;      #文件扩展名与文件类型映射表
    default_type  application/octet-stream;  #默认文件类型，默认为text/plain

​```
sendfile        on;   #允许sendfile方式传输文件，默认为off，可以在http块，server块，location块。
keepalive_timeout  65;  #连接超时时间，默认为75s，可以在http，server，location块。
server {
    listen       8513; 			 #监听端口		
    server_name  192.168.3.123; 	#监听地址    这个是我自己本机地址
    autoindex on;
	autoindex_exact_size off;
    root D:/project/workspace/.metadata/.plugins/org.eclipse.wst.server.core/tmp2/wtpwebapps/vs/files;  #定义服务器的默认网站根目录位置

    #定义错误提示页面
    error_page   500 502 503 504  /50x.html;  
    location = /50x.html { 
        root   html;  
    }

    location ~ .flv{
        flv;
    }
    location ~ .mp4{
        mp4;
    }


```

在nginx安装目录下，dos命令  start nginx.exe或者直接双击nginx.exe

访问 192.168.3.123:8513

第一次安装配一下访问端口listen直接localhost进行访问

![](Nginx\a.png)

以下为当前流行web服务器对比

![](Nginx\b.png)

