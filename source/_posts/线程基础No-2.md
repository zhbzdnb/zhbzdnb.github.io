---
title: 线程基础No.2
date: 2019-09-08 20:24:03
tags:
- 多线程
categories:
- 多线程
---

毕竟没把这个书当作工具书，慢慢看 。写个博客加深印象和概念,并对很多专业术语进行熟悉 美滋滋。

,毕竟没把这个书当作工具书，慢慢看 。写个博客加深印象和概念,并对很多专业术语进行熟悉 美滋滋。



### 线程生命周期

每一个线程都有自己的局部变量表、程序计算器、生命周期

生命周期阶段：

#### NEW

当你new一个thread对象时，并没有执行start，此时就new状态

RUNNABLE

new状态通过start进入runnable状态，此时注意这个状态并不是真正的执行状态，而是可执行状态，等待cpu的调度

#### RUNNING

cpu调度轮询分配内存后，由runnable进入running状态，真正的执行状态，也就是开始执行逻辑代码了。

#### BLOCKED

在running状态调用sleep或者wait方法加入了waitSet中，则进入BLOCED状态

#### TERMINATED

此状态是线程的终结，整个生命周期结束。

### 模板设计模式run和start在thead中的应用

观察之前用thead解决一个并发动作的demo，会发现new一个thread并且重写了run方法，再用start启动。

查看thread源码

![1567949805362](线程基础No-2\1567949805362.png)

找到这个target

![1567949867035](线程基础No-2\1567949867035.png)

得到结论：当我们不用runnable接口进行构造那么这个run就是一个空方法，run是线程的执行单元

注意: 不可以启动线程两次，否则会出现IllegalThreadStateException异常

​	     线程启动后会加入到线程组内，不去指定线程组默认组为main的。

 		线程进入terminted状态后，再次尝试启动线程后也会报IllegalThreadStateException异常

这其实是一种模板设计，分析执行下面的代码：

```
public class ThreadTemplatMethod {

    public final void start(String message)
    {
        System.out.println("*********");
        run(message);
        System.out.println("*********");
    }

    protected void run(String message)
    {

    }
    public  static  void  main(String[] args)
    {
        ThreadTemplatMethod t1=new ThreadTemplatMethod()
        {
            @Override
            protected void run(String message) {
                System.out.println("武大郎"+message);
            }
        };
        t1.start("被绿了");
        ThreadTemplatMethod t2=new ThreadTemplatMethod()
        {
            @Override
            protected void run(String message) {
                System.out.println("贾乃亮"+message);
            }
        };
        t2.start("被绿了");
    }

}

```

![1567950705531](线程基础No-2\1567950705531.png)

这个demo的start和run一一对应threa的方法

程序结构由父类控制，而且final修饰不可重写，子类只需要执行想要的逻辑任务就行了

## RUNNINGABLE接口

java为了解决共享资源这个问题，弄出了runningable接口，将线程控制和业务逻辑分离开来；

### 共享资源问题

使用代码简单呈现此问题

场景 某公司有四个人事，同时进行java招聘，公司只需要50个人

设计一个人事对象线程

启动并查看结果

![1569076122481](线程基础No-2\1569076122481.png)

这个index不唯一，为了解决给他加static关键字

查看结果 index的确唯一

![1569076437966](线程基础No-2\1569076437966.png)

但这样并非线程安全，多个类都去访问更新。为了彻底解决此问题需要用的**资源同步**的知识之后专门写一篇分析学习。

### 使用runnable接口解决资源共享问题

使用同一runnable接口构造不同Thread实例

![1569080006899](线程基础No-2\1569080006899.png)

可以看到这样的效果和static是一样的

查看RUNNUINGABLE接口

![1569080135757](线程基础No-2\1569080135757.png)

这个就一个无参的run方法 擦。。

对比之前的Thead类源码，

![1569080260540](线程基础No-2\1569080260540.png)

![1569159007564](线程基础No-2\1569159007564.png)

这个thread类的run方法其实也就是实现runningable的run

创建线程的两种方式 继承Thread类 重写run方法；实现runningable的run方法，并将实例作为创建Thread的构造参数；

两者都是为了实现将线程控制和业务逻辑解耦分离的。

### RUNNINGABLE的策略设计模式

看一个jdbc开发的例子





![IMG_20190922_220221](线程基础No-2\IMG_20190922_220221.jpg)

具体怎么去操作数据库的这部分你不用管，你只要考虑查出来的数据怎么封装就好了



这本书看了快半年了,毕竟没把这个书当作工具书，慢慢看 。写个博客加深印象和概念,并对很多专业术语进行熟悉 美滋滋。