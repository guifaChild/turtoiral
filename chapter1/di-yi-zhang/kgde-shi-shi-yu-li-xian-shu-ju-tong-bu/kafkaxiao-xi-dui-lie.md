# Kafka消息队列

---

Kafka是一个分布式的流媒体平台，在Kafka中，客户端和服务器之间的通信是通过简单，高性能，语言无关的TCP协议完成的

## Kafka的三个关键功能

发布和订阅记录流，类似于消息队列或企业消息的传递系统。

以容错的持久方式存储记录流。

记录发生时处理流

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

