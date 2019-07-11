---
title: 当面试官问道为什么要使用你项目中的Spring+SpringMvc+Mybatis
date: 2019-01-01 17:04:23
tags: 
- 面试
categories:
- 面试
---
&ensp;&ensp;在面试中不管这个面试官对框架技术是否上道，他在你对项目使用技术进行描述后经常会问如:你为什么要使用Spring+SpringMVc+Mybatis又或者说这几个框架的优点？
&ensp;&ensp;答：mybatis底层实现了JDBC，是对JDBC的封装。使用mybatis可以吧sql写在xml文件中，便于统一管理和优化，解除sql和程序代码的耦合。提供映射标签，支持对象和和数据库orm字段关系的映射，支持对象关系映射标签，支持对象关系的组建。提供xml标签，支持编写动态sql。
&ensp;&ensp;&ensp;&ensp;使用Spring可以通过Spring的IOC特性，将对象之间的依赖关系交给了Spring控制，方便解耦，简化了开发；通过Spring的AOP特性，很容易实现事务，日志，权限的控制；提供了对其他优秀开源框架的集成支持。
&ensp;&ensp;&ensp;&ensp;springMVC是使用了MVC设计思想的轻量级web框架，对web层进行解耦，是的我们开发更简洁；与Spring无缝衔接；灵活的数据验证，格式化，数据绑定机制。