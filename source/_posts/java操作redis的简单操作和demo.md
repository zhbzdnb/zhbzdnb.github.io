---
title: java操作redis的简单操作和demo
date: 2019-10-07 03:39:50
tags:
- redis
categories:
- redis
---

redis基本安装和语法

https://www.runoob.com/redis/redis-install.html

https://blog.csdn.net/qq_39207177/article/details/83621837

redis密码windows直接改redis.windows.conf这个配置文件的requirepass参数

使用java代码将redis引入项目登录验证码验证的小demo，有很多缺陷，只用于学习.

```
     <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>2.9.0</version>
     </dependency>
```



```
package Redis;

import org.junit.Test;
import redis.clients.jedis.Jedis;
import tool.IPTimeStamp;

import java.util.Random;

public class RedisDemo {

    public Jedis ConnectRedis()
    {
        Jedis jedis = null;
        try {
            jedis = new Jedis("127.0.0.1",6379);
            jedis.auth("123456");
            //测试连接（打印pong就算连接成功）
            System.out.println(jedis.ping());
        } catch (Exception e) {
            e.printStackTrace();
        }
        return jedis;

    }
    //模拟某IP进入登录页面生成验证码 并记录到数据库 ip这里demo随意传值了
    public String insertRedis(String ip)
    {
        String VerificationCode=null;
        try {
            String str="ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789";
            StringBuilder sb=new StringBuilder(4);
            for(int i=0;i<4;i++)
            {
                char ch=str.charAt(new Random().nextInt(str.length()));
                sb.append(ch);
            }
            VerificationCode=sb.toString();
            //插入数据
            Jedis jedis=ConnectRedis();
            jedis.set(ip,VerificationCode);
            //这里将其数据插入数据 省略
        } catch (Exception e) {
            e.printStackTrace();
        }
        return VerificationCode;
    }
    //模拟登录验证 验证码
    public String verificationMark(String mark,String ip)
    {
        String code="success";
        //第一次验证使用数据验证  其他未点刷新再次验证使用redis验证
        Jedis jedis=ConnectRedis();
        if(1==2)
        {
            //验证成功删除redis数据
            jedis.del(ip);
        }else {
            if(jedis.get(ip)!=null&&mark.equals(jedis.get(ip)))
            {
                jedis.del(ip);
                //这里数据库删除此ip的验证码
            }else {
                code="error";
             }
        }
        return code;
    }
    public static void main(String args[]) {
            //模拟进入首页
            String ip = "192.168.3.22";
            RedisDemo rd=new RedisDemo();
            //这里用于测试直接将验证码赋值
            String mark= rd.insertRedis(ip);
            //点击登录之后
            if (rd.verificationMark(mark, ip).equals("success")) {
                System.out.println("验证成功");
            } else {
                System.out.println("验证失败");
            }

    }
   }

```

redis学习的重点还是放在其策略模式、简单的调用操作很容易上手的.