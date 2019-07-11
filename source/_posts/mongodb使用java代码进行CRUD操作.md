---
title: mongodb使用java代码进行CRUD操作
date: 2019-07-08 21:31:52
tags:
- mongodb
categories:
- mongodb
---



## mongodb数据库组件关系

角色>用户>数据库>集合>文档



## 创建一个root超级管理员用户

```
>use admin
#注意如果开启的安全验证要先登录一个账号后才能创建
>db.createUser(
... ... ...  {
... ... ...     user: "zhbroot",
... ... ...    pwd: "zhbroot",
... ... ...     roles: [
... ... ...       { role: ["root"] },
... ... ...    ]
... ... ...  }
... ... ...  )
#验证创建是否成功 
>db.auth("zhbroot","zhbroot")
```

转到windows shell

```
#转到Mongodb bin目录下
>mongo --port 27017 -u "zhbtest" -p "zhbtest" --authenticationDatabase "admin"

报错：
	mongo : 无法将“mongo”项识别为 cmdlet、函数、脚本文件或可运行程序的名称。请检查名称的拼写，如果包括路径，请确保路径正
确，然后再试一次。
所在位置 行:1 字符: 1
+ mongo --port 27017 -u "zhbroot" -p "zhbroot" --authenticationDatabase ...
+ ~~~~~
    + CategoryInfo          : ObjectNotFound: (mongo:String) [], CommandNotFoundException
    + FullyQualifiedErrorId : CommandNotFoundException


Suggestion [3,General]: 找不到命令 mongo，但它确实存在于当前位置。默认情况下，Windows PowerShell 不会从当前位置加载命令 。如果信任此命令，请改为键入“.\mongo”。有关详细信息，请参阅 "get-help about_Command_Precedence"。
    
    
 
根据提示修改代码
> .\mongo --port 27017 -u "zhbtest" -p "zhbtest" --authenticationDatabase "admin"

#键入成功 进入mongodb shell

#新建一个数据库
>use zhbdata
#只有在此数据库有更改才会创建
#创建一个集合 user 
db.createCollection("user",{capped:true,autoIndexId:true})
```



## java使用安全性密码登录mongodb

为了方便之后的crud操作我将这个弄成工具类

```

import com.mongodb.MongoClient;
import com.mongodb.MongoCredential;
import com.mongodb.ServerAddress;
import com.mongodb.client.MongoDatabase;

import java.util.ArrayList;
import java.util.List;

public class MongoDBUtil {


    //需要密码认证方式连接
    public static MongoDatabase getConnect(){

            //连接到MongoDB服务 如果是远程连接可以替换“localhost”为服务器所在IP地址
            //ServerAddress()两个参数分别为 服务器地址 和 端口
            ServerAddress serverAddress = new ServerAddress("localhost",27017);
            List<ServerAddress> addrs = new ArrayList<ServerAddress>();
            addrs.add(serverAddress);

            //MongoCredential.createScramSha1Credential()三个参数分别为 用户名 数据库名称 密码
        MongoCredential credential = MongoCredential.createScramSha1Credential("zhbroot", "admin", "zhbroot".toCharArray());

            List<MongoCredential> credentials = new ArrayList<MongoCredential>();
            credentials.add(credential);

            //通过连接认证获取MongoDB连接
            MongoClient mongoClient = new MongoClient(addrs,credentials);

            //连接到数据库
            MongoDatabase mongoDatabase = mongoClient.getDatabase("zhbdata");
            System.out.println("Connect to database successfully");


           //返回连接数据库对象
           return mongoDatabase;
    }

}

```



## 开始CRUD操作

#### 创建集合

```
 //创建集合
    @Test
    public void mongodbcreateCollection(){
        try{
            //获取数据库连接对象
            MongoDatabase mongoDatabase = MongoDBUtil.getConnect();
            //创建集合
            mongoDatabase.createCollection("usertest");
            System.out.println("集合创建成功");

        }catch(Exception e){
           e.printStackTrace();
        }

    }
```

#### 插入一个文档

```
    @Test
    public void insertOneTest(){
        //获取数据库连接对象
        MongoDatabase mongoDatabase = MongoDBUtil.getConnect();
        //获取集合
        MongoCollection<Document> collection = mongoDatabase.getCollection("usertest");
        //要插入的数据
        Document document = new Document("name","张三")
                .append("sex", "男")
                .append("age", 18);
        //插入一个文档
        collection.insertOne(document);

    }
```

#### 删除筛选的文档

```
    @Test
    public void deleteOneTest(){
        //获取数据库连接对象
        MongoDatabase mongoDatabase = MongoDBUtil.getConnect();
        //获取集合
        MongoCollection<Document> collection = mongoDatabase.getCollection("usertest");
        //申明删除条件
        Bson filter = Filters.eq("age",18);
        //删除与筛选器匹配的单个文档
        collection.deleteOne(filter);
    }
```

#### 查看所有文档

```
    //查找集合中的所有文档
    @Test
    public void findTest() {
        //获取数据库连接对象
        MongoDatabase mongoDatabase = MongoDBUtil.getConnect();
        //获取集合
        MongoCollection<Document> collection = mongoDatabase.getCollection("usertest");
        //查找集合中的所有文档
        FindIterable findIterable = collection.find();
        MongoCursor cursor = findIterable.iterator();
        while (cursor.hasNext()) {
            System.out.println(cursor.next());
        }
    }
```

#### 条件查询

```
    //条件查询
    @Test
    public void FilterfindTest(){
        //获取数据库连接对象
        MongoDatabase mongoDatabase = MongoDBUtil.getConnect();
        //获取集合
        MongoCollection<Document> collection = mongoDatabase.getCollection("usertest");
        //指定查询过滤器
        Bson filter = Filters.eq("name", "张三");
        //指定查询过滤器查询
        FindIterable findIterable = collection.find(filter);
        MongoCursor cursor = findIterable.iterator();
        while (cursor.hasNext()) {
            System.out.println(cursor.next());
        }
    }
```

