---
title: 使用ConcurrentLinkedQueue安全队列解决集合队列边写边读报错
date: 2019-05-26 15:31:10
tags:
- java集合框架
categories:
- java集合框架
---

此对象多用于解决多并发的消息队列，使用平常的集合对象 同时对此队列高并发进行读写，会出现数据不准确，亦或者报下标越界的错误。自己写了个2读2写的多线程demo 使用arraylist 多次出现数据不准确，以及报错。使用ConcurrentLinkedQueue完美的解决了问题，此对象需注意，遍历如果使用size() 效率会大大降低，应使用isEmpty()进行遍历。

关于此对象的方法的基本使用:<https://www.cnblogs.com/pony1223/p/9509205.html>