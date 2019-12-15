---
title: rocket简单环境部署+视图化管理+简单测试工具类
date: 2019-12-15 23:33:08
tags: - mq
categories: - mq
---

## 环境部署

依赖环境为java 建议1.8+

须知rocketmq需要启动两个东西 

**一个 broker 消息存储**

**一个是namesrv 服务管理**

[下载地址]( http://rocketmq.apache.org/release_notes/ ) 

下载解压后部署环境变量

![1576424460505](rocket简单环境部署-视图化管理-简单测试工具类\1576424460505.png)

因为启动namesrv时需要这个环境变量

## 启动服务

### 启动namesrv

进入解压路径 双击 mqnamesrv.cmd 或者cmd start mqnamesrv.cmd 

##### 注意！！！

rocketmq默认是9876端口，而且不像tomcat之类的一样直接改配置文件就行；

个人喜欢对软件全面控制，所以我一开始就指定的非默认端口，搞得我弄了好久，坑！

百度一看，好家伙源码里面直接用的死端口，这他娘是谁出的注意。

[百度地址](https://zhidao.baidu.com/question/2141928956035760308.html)

![1576424967038](rocket简单环境部署-视图化管理-简单测试工具类\1576424967038.png)

还好后面找到了方法，他们还好留了一手，启动时指点参数 -c 配置文件路径 指定端口

文件内容![1576424895637](rocket简单环境部署-视图化管理-简单测试工具类\1576424895637.png)

文件名namesrv.txt ,properties也行一样

因为我直接放在mqnameserv.cmd的bin目录下

所以指定namesrv端口启动命令 bin目录下 start mqnameserv.cmd -c nameser.txt

![1576425100313](rocket简单环境部署-视图化管理-简单测试工具类\1576425100313.png)

如图启动成功

### 启动broker

bin目录下

start mqbroker.cmd -n 127.0.0.1:9876 autoCreateTopicEnable=true 

设置可手动创建topic 

autoCreateTopicEnable=true 

如图成功

![1576425249889](rocket简单环境部署-视图化管理-简单测试工具类\1576425249889.png)

## 视图化web项目安装部署

安装这个对初学者帮助很大，操作简单

github炸了

[所以用码云的项目地址](https://gitee.com/mirrors/RocketMQ-Externals/blob/master/README.md)

下载zip包 或者clone

### 项目基本配置

 下载完成之后，进入 rocketmq-externals\rocketmq-console\src\main\resources\application.properties 进行配置 

![1576426623555](rocket简单环境部署-视图化管理-简单测试工具类\1576426623555.png)

一个项目访问端口

一个rocket的端口

### 将项目编译

 进入 rocketmq-externals\rocketmq-console 文件夹，

执行 `mvn clean package -Dmaven.test.skip=true`

因为是maven项目 需要配制一下maven环境变量

然后就是阿里云镜像库 大家都懂

### 运行插件

 进入 target 文件夹，执行 `java -jar rocketmq-console-ng-1.0.1.jar` 

什么版本执行什么版本的jar包 现最新是1.0.1 项目地址有中文文档可以看看

浏览器访问 http://localhost:8080

项目报错问题容易解决;

![1576426921674](rocket简单环境部署-视图化管理-简单测试工具类\1576426921674.png)

查看默认消费者是否存在，没有那么就代表rocket启动有问题或者项目端口指向有问题；

没问题下一步。

### 简单示例代码

引入maven依赖

```
 <dependency>
        <groupId>org.apache.rocketmq</groupId>
        <artifactId>rocketmq-client</artifactId>
        <version>4.6.0</version>
    </dependency>
```

生产者

```
package RocketMq;

import org.apache.rocketmq.client.exception.MQClientException;
import org.apache.rocketmq.client.producer.DefaultMQProducer;
import org.apache.rocketmq.client.producer.SendCallback;
import org.apache.rocketmq.client.producer.SendResult;
import org.apache.rocketmq.common.message.Message;
import org.apache.rocketmq.remoting.common.RemotingHelper;


public  class ProducerTool {
    private static DefaultMQProducer producermq;


    private ProducerTool(){}

    /**
    * @Description: 启动
    * @Param: * @param MQ_producerGroup
    * @return: org.apache.rocketmq.client.producer.DefaultMQProducer
    * @Author: 虾
    * @Date: 2019/12/16 0:39
    */
    public  static DefaultMQProducer ProducerMqstart(String MQ_producerGroup)  throws MQClientException
    {
        producermq = new DefaultMQProducer(MQ_producerGroup);

        producermq.setNamesrvAddr("127.0.0.1:1111");

        producermq.start();

        return producermq;
    }



    /**
    * @Description: 同步发送消息
    * @Param: * @param producermq
     * @param topic
     * @param tags
     * @param message
    * @return: void
    * @Author: 虾
    * @Date: 2019/12/16 0:39
    */
    public static void SyncProducer(DefaultMQProducer producermq,String topic,String tags,String message) throws Exception
    {


        for (int i = 0; i < 5; i++) {
            //Create a message instance, specifying topic, tag and message body.
            Message msg = new Message(topic/* Topic */,
                    tags /* Tag */,
                    (message + i).getBytes(RemotingHelper.DEFAULT_CHARSET) /* Message body */
            );
            //Call send message to deliver message to one of brokers.
            SendResult sendResult = producermq.send(msg);
        }
        //Shut down once the producer instance is not longer in use.
        System.out.println("发送完毕");
    }

    /**
    * @Description: 异步发送消息
    * @Param: * @param producermq
     * @param topic
     * @param tags
     * @param msgkey
     * @param message
    * @return: void
    * @Author: 虾
    * @Date: 2019/12/16 0:39
    */
    public static void AsyncProducer(DefaultMQProducer producermq,String topic,String tags,String msgkey,String message) throws Exception
    {

        producermq.setRetryTimesWhenSendAsyncFailed(0);
        for (int i = 0; i < 100; i++) {
            final int index = i;
            //Create a message instance, specifying topic, tag and message body.
            Message msg = new Message(topic, tags, msgkey, message.getBytes(RemotingHelper.DEFAULT_CHARSET));
            producermq.send(msg, new SendCallback() {

                public void onSuccess(SendResult sendResult) {
                    System.out.printf("%-10d OK %s %n", index,
                            sendResult.getMsgId());
                }

                public void onException(Throwable e) {
                    System.out.printf("%-10d Exception %s %n", index, e);
                    e.printStackTrace();
                }
            });
        }
        //Shut down once the producer instance is not longer in use.
       
    }


    /**
    * @Description: 传输
    * @Param: * @param producermq
     * @param topic
     * @param tags
     * @param message
    * @return: void
    * @Author: 虾
    * @Date: 2019/12/16 0:38
    */
    public static void OnewayProducer(DefaultMQProducer producermq,String topic,String tags,String message) throws Exception
    {
        for (int i = 0; i < 100; i++) {
            //Create a message instance, specifying topic, tag and message body.
            Message msg = new Message(topic,
                    tags , (message + i).getBytes(RemotingHelper.DEFAULT_CHARSET) /* Message body */
            );
            //Call send message to deliver message to one of brokers.
            producermq.sendOneway(msg);

        }
        //Shut down once the producer instance is not longer in use.

    }

    /**
    * @Description: 关闭消费者对象
    * @Param: * @param producermq
    * @return: void
    * @Author: 虾
    * @Date: 2019/12/15 21:02
    */
    public static void shutdownproducer(DefaultMQProducer producermq) throws Exception
    {
        producermq.shutdown();
    }


    public static void main(String[] args) {

        new Thread(new Runnable() {
            public void run() {
                try {
                    DefaultMQProducer producer = ProducerTool.ProducerMqstart("group-producer-xia");
                    ProducerTool.SyncProducer(producer, "topic-xia1", "tags-xia", "虾的第一次尝试");
                    ProducerTool.shutdownproducer(producer);
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }).start();

    }

}


```

消费者

```
package RocketMq;

import org.apache.rocketmq.client.consumer.DefaultMQPushConsumer;
import org.apache.rocketmq.client.consumer.listener.ConsumeConcurrentlyContext;
import org.apache.rocketmq.client.consumer.listener.ConsumeConcurrentlyStatus;
import org.apache.rocketmq.client.consumer.listener.MessageListenerConcurrently;
import org.apache.rocketmq.client.exception.MQClientException;
import org.apache.rocketmq.common.consumer.ConsumeFromWhere;
import org.apache.rocketmq.common.message.Message;
import org.apache.rocketmq.common.message.MessageExt;

import java.util.List;

public  class ConsumerTool {
    private static DefaultMQPushConsumer consumer=new DefaultMQPushConsumer();


    private ConsumerTool(){}

    public static void main(String [] args) throws Exception
    {

        new Thread(() -> {
            try {
                ConsumerTool.ConsumerMqMain("group-consumer-xia","topic-xia1");
            }catch(Exception e){
                e.printStackTrace();
            }
        }).start();




    }

    /**
    * @Description: 推送方式消费者
    * @Param: * @param consumerGroup* @param topic
    * @return: void
    * @Author: 虾
    * @Date: 2019/12/16 0:38
    */
    public  static void ConsumerMqMain(String consumerGroup,String topic)  throws MQClientException
    {
        System.out.println("消费者启动");
         consumer = new DefaultMQPushConsumer(consumerGroup);

        consumer.setNamesrvAddr("127.0.0.1:1111");

        //订阅 名为XiaTopic下所有tag
        consumer.subscribe(topic,"*");
        //订阅指定Topic 指定tag写法 consumer.subscribe("XiaTopic","tag名");
        //订阅指定Topic 多个tag用||分割  consumer.subscribe("XiaTopic","taga||tagb||tagc");

        //设置从头开始消费CONSUME_FROM_FIRST_OFFSET  或者从尾部开始消费CONSUME_FROM_LAST_OFFSET
        consumer.setConsumeFromWhere(ConsumeFromWhere.CONSUME_FROM_FIRST_OFFSET);

        //可以修改每次消费消息的数量，默认设置是每次消费一条
        // consumer.setConsumeMessageBatchMaxSize(10);

        //注册消费的监听
        consumer.registerMessageListener(new MessageListenerConcurrently() {

            //在此监听中消费信息，并返回消费的状态信息
            public ConsumeConcurrentlyStatus consumeMessage(
                    List<MessageExt> msgs,
                    ConsumeConcurrentlyContext context) {

                for(Message msg:msgs){

                    System.out.println(new String(msg.getBody()));
                }

                // msgs中只收集同一个topic，同一个tag，并且key相同的message
                // 会把不同的消息分别放置到不同的队列中
                return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
            }
        });

        consumer.start();
    }


}


```

执行结果：

![1576428061049](rocket简单环境部署-视图化管理-简单测试工具类\1576428061049.png)

![1576428085913](rocket简单环境部署-视图化管理-简单测试工具类\1576428085913.png)

## 报错No route info of this topic

1. 引入的maven包版本要和rocket的版本一致
2. 端口问题  我自己就因为不用默认端口高了很久
3. netty包冲突

## 查看rocketmq的日志

当出现报错 一头雾水时 我们需要日志 

查看rocketmq的配置文件

![1576427713198](rocket简单环境部署-视图化管理-简单测试工具类\1576427713198.png)

里面关于日志的都是 {user.home}/logs/rocketmqlogs/xxxx

直接将user.home改成想要路径 完事~

