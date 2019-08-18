---
title: 使用python操作mongodb进行crud
date: 2019-08-18 17:46:14
tags:
- python
- mongodb
categories:
- python
- mongodb
---

直接上代码吧，自身配合资料敲来的。用的python2.7。3x的可能语法会有改变

```
# coding=utf-8
import pymongo
# 创建连接

# 无安全性连接
# client = pymongo.MongoClient(host="localhost", port=27017)

# 安全性连接

host = 'localhost'
client = pymongo.MongoClient(host, 27017)
# 连接mydb数据库,账号密码认证
db = client["admin"]
db.authenticate("zhbroot", "zhbroot")

# 打印当前管理员数据库下所有数据库
print(client.list_database_names())

# 切换至专用的存储数据的数据库
db = client["zhbdata"]

# 打印当前数据库所有的集合
print(db.list_collection_names(session=None))

# 连接到指定集合
collection = db.usertest

# 创建数据
data_1 = {'name': 'zzzzz',
          'age': 27,
          'sex': '男'}

# 两个方法都能插入 区别:save新增时方法检测到当前插入的是已经存在的数据对象时执行update，insert会报错；但insert可以同时插入一个列表，save要遍历插入，因此insert效率高
collection.insert(data_1)
collection.save(data_1)

record_1 = [
           {'name': 'ak47', 'age': 27, 'sex': '男'},
           {'name': 'qabcd', 'age': 22, 'sex': '女'},
           {'name': '罗x', 'age': 20, 'sex': '男'},
           {'name': 'lose', 'age': 33, 'sex': '男'}
          ]
collection.insert(record_1)

# 删除

# 删除所有满足条件的文档,删除年龄大于26，小于100
# collection.delete_many({'age':{'$gt':6,'$lt':100}})
# 删除一条满足条件的文档,删除age=27
collection.delete_one({'age':27})

# collection.delete_many({}) #删除整个集合


# 更改

# replace_one用指定的key-value替代原来所有的key-value
collection.replace_one({'age': 30}, {'comment': 'update记录'})
# update_one更新已经对应的key-value，其它不变
collection.update_one({'age': { '$gt': 20}}, {'$set': {"comment": "更新于跟新年龄20岁的一条记录"}})
# 同上，能够update所有符合匹配条件的文档
collection.update_many({'age': {'$gt': 21}}, {'$set': {'comment': '更新于跟新年龄20岁的s所有记录'}})

# 查询 查不到返回none

# 查询所有
print("-------------------------查询所有----------------------")
for i in collection.find():
    print(i)
# 条件查询
print("-------------------------条件查询----------------------")
for i in collection.find({"name":"张三"}):
    print(i)
print(collection.find_one({"name":"李四"}))

```

