---
title: 线程基础No.1
date: 2019-08-19 22:29:45
tags:
- 多线程
categories:
- 多线程
---

##### 前言

多线程不必多说，要向高级开发工程师必要的一个东西。个人认为，多线程高并发这种生成环境多用于电子商城、学生管理系统、以及和金融挂钩的项目。一般的物联网管理类型的项目很少有这种场景。买了本多线程的书，现将觉得精华的东西记录。

### 认识线程

现所有操作系统都是多任务运行，任务即进程，而每一个进程就至少有一个线程.

线程是程序执行的路径，每个线程都有私有的**局部变量表、程序计算器、生命周期**。



##### 快速创建启动线程

写一个模拟日常动作的类，模拟一边吃一边玩。

```
  //模拟一个生活场景

    //吃东西
   public static void eat() throws Exception
   {
       for (;;)
       {
           System.out.println("吃了一口薯片");
           TimeUnit.SECONDS.sleep(1);
       }
   }

   //玩手机
   public static void lookB() throws Exception
   {
       for (;;)
       {
           System.out.println("看了看b站");
           TimeUnit.SECONDS.sleep(1);
       }
   }
```

注意：TimeUnit是JKD1.5后加入的一个枚举,对sleep方法进行了很好的封装用其可以省去很多换算时间的活；

以下是基本方法。

![1566226738964](线程基础No-1\1566226738964.png)

回到主题，使用非线程的方法进行并行动作

```
 public static  void main(String[] args)throws Exception
   {
       //正常程序执行
		lookB();
		eat();
   }
```

有点蠢逼。。

看看执行结果

很尴尬死命看b站

![1566227001996](线程基础No-1\1566227001996.png)

使用线程轻松解决，只需要将玩和吃任意一个放进线程中执行:

```
 public static  void main(String[] args)throws Exception
   {
       //正常程序执行
     /*  lookB();
       eat();*/

       //线程解决边看边吃
       new Thread()
       {
           public void run()  {
               try {
                   lookB();
               }catch (Exception e)
               { }
           }
       }.start();
       eat();
   }
```

查看结果

![1566227283877](线程基础No-1\1566227283877.png)

#### 使用VisualVM、jconse、jstack命令观察线程

由于这三都是jdk自带的，去jdkbin搜索即可

![1566725290307](线程基础No-1\1566725290307.png)

idea下使用 先安装插件![1566727536114](线程基础No-1\1566727536114.png)

重启后进行配置

我的idea2018的

俩个配置一个是配置VisualVM的路径

一个是jdk的

配置重启restart

![1566742396472](线程基础No-1\1566742396472.png)

有这个橘色标配置成功，随便run一个线程。

自动启动VisualVM，发现报错

![1566742275635](线程基础No-1/1566742275635.png)

翻阅网站说明https://visualvm.github.io/troubleshooting.html#jpswin![1566742483222](线程基础No-1\1566742483222.png)

经过排查我是第一条，通过System.getProperty("java.io.tmpdir")得到temp的路径![1566742527353](线程基础No-1\1566742527353.png)

在此搜索*hsperfdata*

![1566742557294](线程基础No-1\1566742557294.png)

修改设置文件夹权限，重新run  成功
![1566742594709](线程基础No-1\1566742594709.png)

![1566742771496](线程基础No-1\1566742771496.png)

![1566743019560](线程基础No-1\1566743019560.png)

运行对应start
休眠对应的sleep操作
等待对应的waite
驻留对应的线程池里的空闲线程
监视对应的synchronized阻塞

有意思的是，main主线程和我创建的线程thread-0 是在sleep的状态下持续执行的，其他的是一些守护线程，垃圾回收线程等等



jconsole是VisualVM的一个插件，jdk已经内置，启动如果本地连接什么都没有，看我上面的解决。VisualVM里也可以安装配置这个插件，我觉得麻烦没去配置了，直接去启动jdk bin下面的exe就行![1566743666479](线程基础No-1\1566743666479.png)

找到熟悉的main和新建的thred0![1566743765943](线程基础No-1\1566743765943.png)





jstack就更加简单了,用的最多的还是jstack pid

![1566744094890](线程基础No-1\1566744094890.png)

命令基本使用https://blog.csdn.net/fenglibing/article/details/6411940