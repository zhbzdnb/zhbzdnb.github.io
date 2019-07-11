---
title: mongodb安装配置卸载配置安全性使用java访问
date: 2019-07-07 17:21:20
tags:
- mongodb
categories:
- mongodb
---

## 安装

决定进行学习mongodb的时候是在菜鸟教程上面进行安装，真他妈的坑，教你安装直装版，还叫你进行配置，直接安装的版本配置文件、log文件存放路径、数据库存放路径早就意见给安了。

下载连接https://www.runoob.com/mongodb/mongodb-window-install.html

下载msi包 直接安装，无需配置windows服务，安装的时候已经配好。

## 配置MongoDB安全性

mongodb一个让我无语的地方，不会安装的时候要你配置数据库用户密码，安装成功后无需其他操作直接登入数据库，也就是说你不配置安全性，人家轻松给你登上数据库，毫无安全性.

验证安装是否成功

```
#进入安装的bin目录双击mongo.exe
#进入admin管理员状态 注意你use不存在的用户不会报错
use admin
#查看所有角色
show dbs
```

在配置安全性前，在admin角色下创建一个管理员角色

```
db.createUser(
  {
    user: "zhb",
    pwd: "zhb",
    roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
  }
)
```

配置安全性，网上杂七杂八的很多，直装版自带了配置文件 在bin目录 mongod.cfg文件  修改配置：

```
#开启安全性验证
security:
    authorization: enabled
```

验证是否成功；重启mongodb，直装版在安装的时候已经把服务注册到了windows

```
net stop mongodb
net start mongodb
```

打开Mongodb的shell  双击mongo，exe

```
use admin
show dbs
#我这边是无法输入，无效命令
#安全性验证:
db.auth("zhb","zhb")
#正确显示角色
配置成功
```



## 创建读写用户

### 首先看看mongodb数据库角色以及权限

**内建的角色** 
　　　　**数据库用户角色**：read、readWrite; 
　　　　**数据库管理角色**：dbAdmin、dbOwner、userAdmin； 
　　　　**集群管理角色**：clusterAdmin、clusterManager、clusterMonitor、hostManager； 
　　　　**备份恢复角色**：backup、restore； 
　　　　**所有数据库角色**：readAnyDatabase、readWriteAnyDatabase、userAdminAnyDatabase、dbAdminAnyDatabase 
　　　　**超级用户角色**：root // 这里还有几个角色间接或直接提供了系统超级用户的访问（dbOwner 、userAdmin、userAdminAnyDatabase） 
　　　　**内部角色：__system** 


**角色说明：** 
　　　　**Read**：允许用户读取指定数据库 
　　　　**readWrite**：允许用户读写指定数据库 
　　　　**dbAdmin**：允许用户在指定数据库中执行管理函数，如索引创建、删除，查看统计或访system.profile 
　　　　**userAdmin**：允许用户向system.users集合写入，可以找指定数据库里创建、删除和管理用户 
　　　　**clusterAdmin**：只在admin数据库中可用，赋予用户所有分片和复制集相关函数的管理权限。 
　　　　**readAnyDatabase**：只在admin数据库中可用，赋予用户所有数据库的读权限 
　　　　**readWriteAnyDatabase**：只在admin数据库中可用，赋予用户所有数据库的读写权限 
　　　　**userAdminAnyDatabase**：只在admin数据库中可用，赋予用户所有数据库的userAdmin权限 
　　　　**dbAdminAnyDatabase**：只在admin数据库中可用，赋予用户所有数据库的dbAdmin权限。 
　　　　**root**：只在admin数据库中可用。超级账号，超级权限



### 创建读写用户

```
>use admin


>db.auth("zhb","zhb")

#先进入zhb_readwrite角色 因为在admin角色下无法创建zhb_readwrite角色的数据库
>use zhb_readwrite 

>db.createUser(
... ... ...  {
... ... ...     user: "readwrite",
... ... ...    pwd: "readwrite",
... ... ...     roles: [
... ... ...       { role: "readWrite", db: "zhb_readwrite" },
... ... ...    ]
... ... ...  }
... ... ...  )
>use zhb_readwrite


```

关闭mongo.exe,再次打开

```
>use zhb_readwrite
>db.auth("readwrite","readwrite")
#新增
>db.test.insert({name:"kite"})
#查看
>db.test.find()

```

## MongoDB卸载

直装版再次运行安装程序即可。

## 使用java代码连接mongodb数据库

### 先导入jar包



访问https://mongodb.github.io/mongo-java-driver/ 获得下载连接以及maven支持



### 无密码直接连接



```
    @Test
    public void MongoDBserver()
    {
        try{
            // 连接到 mongodb 服务
            MongoClient mongoClient = new MongoClient( "localhost" , 27017 );

            // 连接到数据库
            MongoDatabase mongoDatabase = mongoClient.getDatabase("mongodbFist");
            MongoCollection<Document> collection = mongoDatabase.getCollection("test");
            System.out.println("连接成功");
        }catch(Exception e){
            e.printStackTrace();
        }
    }
```





