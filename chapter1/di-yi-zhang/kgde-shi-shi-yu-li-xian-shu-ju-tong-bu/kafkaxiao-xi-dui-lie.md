# Kafka消息队列

---

Kafka是一个分布式的流媒体平台，在Kafka中，客户端和服务器之间的通信是通过简单，高性能，语言无关的TCP协议完成的

## Kafka的三个关键功能

发布和订阅记录流，类似于消息队列或企业消息的传递系统。

以容错的持久方式存储记录流。

记录发生时处理流

## [kafka的作用](https://www.jianshu.com/p/1ae35123329e)

## Kafka的几个概念

Kafka作为一个集群运行在一个或斗个可夸多个数据中心的服务器上。

Kafka集群以称为Topic的类别存储记录流。

## Kafka四大核心API

* ### Producer API: 允许应用程序发布的记录流至一个或多个Kafka的Topic（topic即一个主题）
* ### Consumer API：允许应用程序订阅一个或多个Topic，并处理他们记录的数据流
* ### Stream API： 允许应用程序充当流处理器，从一个或多个主题消费的输入流，并产生一个输出流至一个或多个输出主题，有效地
* ### Connector API: 允许构建和运行Kafka Topic 丽娜姐到现有的应用程序或数据系统中重用生产者或消费者。例如，关系数据库中的连

## Kafka Topic

发布记录的类别或订阅源名称

一个Topic可以有零个，一个或多个消费者订阅它的数据

## Kafka Topic Partition

每个分区都是一个有序的，不可变的记录序列

集群持久保存所有已发布的记录（数据，无论是否已使用）可使用配置设置保留期限

## Kafka单机部署

官方链接：（apache kafka）[https://kafka.apachecn.org/quickstart.html](https://kafka.apachecn.org/quickstart.html)

* 文件下载

```
文件下载路径：http://kafka.apache.org/downloads
```

* 文件解压

```
tar -xvf kafka_2.11-2.0.0.tgz909
```

* 进入文件

```
cd kafka_2.11-2.0.0
```

* 启动zookeeper 

```
bin/zookeeper-server-start.sh config/zookeeper.properties
```

* 启动Kafka本身的服务

```
bin/kafka-server-start.sh config/server.properties
```

* 创建topic

```
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic mytest
```

* 查看topic

```
bin/kafka-topics.sh --list --zookeeper localhost:2181
```

* 启动生产者

```
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic my_test
```

* 启动消费者

```
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic my_test --from-beginning
```

可以正式进行测试

## Kafka集群部署

[kafka集群环境部署](https://blog.csdn.net/xuesp/article/details/88094326)

# Kafka 的Java API使用

1、打开IDEA 新建一个Maven项目

```
groupid  : com.zhang.ai.kg

artifactid: kafka-demo
```

2、配置引入的pom.xml文件

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.zhang.ai.kg</groupId>
    <artifactId>kafkademo</artifactId>
    <version>1.0-SNAPSHOT</version>
    <dependencies>
        <dependency>
            <groupId>org.apache.kafka</groupId>
            <artifactId>kafka-clients</artifactId>
            <version>2.0.0</version>
        </dependency>
    </dependencies>

</project>
```

3、创建ConsumerDemo.java 消费者

```java
package com.sft.ai.kafka;

import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.KafkaConsumer;

import java.util.Arrays;
import java.util.Properties;

public class ConsumerDemo {
    public static void main(String[] args) {
//        配置文件内容直接写在代码中
        Properties properties =new Properties();
        //可以写如多个的kafka集群
        properties.put("bootstrap.servers","localhost:9092");
        //用来表示consumer进程所在组的一个字符串，如果设置同样的groupid，表示这些进程属于同一个consumer group
        properties.put("group.id","test");
        //如果设置为真，consumer所接受到的消息的offset将会自动同步到zookeeper里面
        properties.put("enable.auto.commit","true");
        //consumer向zookeeper提交offset的频率 单位是秒
        properties.put("auto.commit.interval","1000");
        //序列化的配置
        properties.put("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
        //序列化的配置
        properties.put("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");


        KafkaConsumer <String,String> consumer= new KafkaConsumer<String,String> (properties);

        consumer.subscribe(Arrays.asList("my_test"));//这里面就是要消费的topic

        while (true){
            ConsumerRecords<String,String> records =consumer.poll(100);//阻塞的最长时间

            for(ConsumerRecord<String,String> record:records){
                System.out.printf("offset =%d ,key= %s , value = %s",record.offset(),record.key(),record.value());
            }
        }

    }
}
```

4、创建ProducerDemo.java 生产者

```java
package com.sft.ai.kafka;

import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.Producer;
import org.apache.kafka.clients.producer.ProducerRecord;

import java.util.Properties;

public class ProducerDemo {
    public static void main(String[] args) {
        Properties properties=new Properties();
        properties.put("bootstrap.servers","localhost:9092");
        //生产者需要server接收到数据之后， 要发出一个确认接受的信号
        //0producer不需要等待任何确认的消息
        //1 至少要等待leader已经成功将数据写入到本地log没并不意味着所有的follower已经写入
        //all  这意味着leader需要等待所有的备份都成功的写入到日志中

        properties.put("acks","all");
        //重试的次数
        //比如有两条消息 ： 1 和 2  1先来，但是1发送失败了，重试次数为1， 2会接着就发送数据，然后2再发送一次
        //改变了我们消息发送的顺序
        properties.put("retries",0);

        properties.put("buffer.memory",33554432);
        //producer试图批处理消息记录，目的是减少请求次数，
        // 改善客户端和服务端的性能
        //是控制批处理消息的字节数
        //如果这个数小，是不是就降低了吞吐量，如果设置为0，那么就是禁用批处理
        //如果这个数设置的很大，是不是就会浪费很多的内存空间。

        properties.put("batch.size",16384);

        properties.put("linger.ms",1);//延时
        properties.put("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
        //序列化的配置
        properties.put("value.serializer","org.apache.kafka.common.serialization.StringSerializer");

        Producer<String,String> producer =new KafkaProducer<String, String>(properties);

        for (int i=0;i<100 ;i++){
            producer.send(new ProducerRecord<String, String>("mytest",Integer.toString(i+1),Integer.toString(i)));

        }
        producer.close();


    }
}

```

5、启动消费者



6、启动生产者

