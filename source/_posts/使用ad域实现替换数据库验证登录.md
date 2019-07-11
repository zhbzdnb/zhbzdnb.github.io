---
title: 使用ad域实现替换数据库验证登录
date: 2019-03-24 11:43:03
tags: 
- ad域
categories:
- ad域
---

前言：前几天老大跟我提了下客户需求，说要用ad域替换数据库用户的相关操作，我也是一脸懵逼。。

## ad域是什么？

域英文叫DOMAIN---- 域(Domain)是Windows网络相关信息中独立运行的单位，域之间相互访问则需要建立信任关系(即Trust Relation)。当一个域与其他域建立了信任关系后，2个域之间不但可以按需要相互进行管理，还可以跨网分配文件和打印机等设备资源，使不同的域之间实现网络资源的共享与管理。 ---- 而Windows的用户/组分为全局用户/组与本地用户/组。 通俗的说如果把人分组，就有各个部门，把人和机器加起来，再分组，一堆一堆..。

### 如何配置ad域？

当前用于测试，一般都是在虚拟机上安一个win服务器系统，这里我用的是[windos server 2008 r2](http://www.ddooo.com/softdown/84930.htm)

[配置过程](https://www.sohu.com/a/166462698_99973431)

注意虚拟机使用桥接模式，自己电脑ping下虚拟机ip.

虚拟机需要配置ip地址，自己写死.

### 测试代码

```
import java.util.Hashtable;

import javax.naming.AuthenticationException;
import javax.naming.Context;
import javax.naming.directory.DirContext;
import javax.naming.directory.InitialDirContext;

public class AdLogin {
    public static void main(String[] args) {
        String userName = "firstname.lastname";//AD域认证，用户的登录UserName
        String password = "user_password";//AD域认证，用户的登录PassWord
        String host = "10.0.0.1";//AD域IP，必须填写正确
        String domain = "@domain.com";//域名后缀，例.@noker.cn.com
        String port = "389"; //端口，一般默认389
        String url = new String("ldap://" + host + ":" + port);//固定写法
        String user = userName.indexOf(domain) > 0 ? userName : userName
                + domain;//网上有别的方法，但是在我这儿都不好使，建议这么使用
        Hashtable env = new Hashtable();//实例化一个Env
        DirContext ctx = null;
        env.put(Context.SECURITY_AUTHENTICATION, "simple");//LDAP访问安全级别(none,simple,strong),一种模式，这么写就行
        env.put(Context.SECURITY_PRINCIPAL, user); //用户名
        env.put(Context.SECURITY_CREDENTIALS, password);//密码
        env.put(Context.INITIAL_CONTEXT_FACTORY,
                "com.sun.jndi.ldap.LdapCtxFactory");// LDAP工厂类
        env.put(Context.PROVIDER_URL, url);//Url
        try {
            ctx = new InitialDirContext(env);// 初始化上下文
            System.out.println("身份验证成功!");
        } catch (AuthenticationException e) {
            System.out.println("身份验证失败!");
            e.printStackTrace();
        } catch (javax.naming.CommunicationException e) {
            System.out.println("AD域连接失败!");
            e.printStackTrace();
        } catch (Exception e) {
            System.out.println("身份验证未知异常!");
            e.printStackTrace();
        } finally{
            if(null!=ctx){
                try {
                    ctx.close();
                    ctx=null;
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }
    }

## }


```

这段代码是网上穿的最多的，我也是测试成功，唯一需要注意的是用户名那段 用户名=用户名+域名