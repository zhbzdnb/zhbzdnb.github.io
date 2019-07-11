---
title: 初识JIN
date: 2019-05-19 16:43:27
tags:
- JNI
categories:
- JNI
---

##### <u>前言</u>

在工作过程中，对接手的项目再次进行研究，发现其重要的一个组件JNI.这个东西说白了就是可以让c、c++语言交互。

### 什么是JNI

JNI是Java Native Interface的缩写，它提供了若干的[API](https://baike.baidu.com/item/API/10154)实现了Java和其他语言的通信（主要是[C](https://baike.baidu.com/item/C/7252092)&[C++](https://baike.baidu.com/item/C%2B%2B)）。从Java1.1开始，JNI标准成为java平台的一部分，它允许Java代码和其他语言写的代码进行交互。JNI一开始是为了本地已[编译](https://baike.baidu.com/item/%E7%BC%96%E8%AF%91/1258343)语言，尤其是C和C++而设计的，但是它并不妨碍你使用其他编程语言，只要调用约定受支持就可以了。使用java与本地已编译的代码[交互](https://baike.baidu.com/item/%E4%BA%A4%E4%BA%92/6964417)，通常会丧失平台[可移植性](https://baike.baidu.com/item/%E5%8F%AF%E7%A7%BB%E6%A4%8D%E6%80%A7/6931884)。但是，有些情况下这样做是可以接受的，甚至是必须的。例如，使用一些旧的库，与硬件、操作系统进行交互，或者为了提高程序的性能。JNI标准至少要保证[本地代码](https://baike.baidu.com/item/%E6%9C%AC%E5%9C%B0%E4%BB%A3%E7%A0%81)能工作在任何Java [虚拟机](https://baike.baidu.com/item/%E8%99%9A%E6%8B%9F%E6%9C%BA)环境。



### JNI可以这样与本地程序进行交互：

1、你可以使用JNI来实现“本地方法”（[native methods](https://baike.baidu.com/item/native%20methods)），并在JAVA程序中调用它们。

2、JNI支持一个“调用接口”（invocation interface），它允许你把一个[JVM](https://baike.baidu.com/item/JVM)嵌入到本地程序中。本地程序可以链接一个实现了JVM的本地库，然后使用“调用接口”执行JAVA语言编写的软件模块。例如，一个用C语言写的浏览器可以在一个嵌入式JVM上面执行从网上下载下来的applets。



### 看了公司里写的jni 。。。尴尬的的是那些同步方法就我现在没有接触到，没调用过，只是在此类初始化了一些配置而已.

  