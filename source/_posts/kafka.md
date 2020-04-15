---
title: Kafka入门（一）
date: 2019-11-04 19:09:54
tags:
---
#1.Kafka的基本概念
## 1.1 生产者（Producer）
生产者，也就是发送消息的一方。生产者负责创建消息，然后将其投递到 Kafka 中。
## 1.2 消费者（Consumer）
消费者，也就是接收消息的一方。消费者连接到 Kafka 上并接收消息，进而进行相应的业务逻辑处理。
## 1.3 Broker
Broker 官方术语就是服务的代理节点，也就是Kafka所谓的队列。对于 Kafka 而言，Broker 可以简单地看作一个独立的 Kafka 服务节点或 Kafka 服务实例。大多数情况下也可以将 Broker 看作一台 Kafka 服务器，前提是这台服务器上只部署了一个 Kafka 实例。一个或多个 Broker 组成了一个 Kafka 集群。一般而言，我们更习惯使用首字母小写的 broker 来表示服务代理节点。
##1.4 Topic
Kafka以topic对消息进行归类，生产者将消息发送到特定的topic，然后消费者到特定的topic消费。
## 1.5 Partition
Topic还可以细分分区（Partition）,同一个主题下的分区所包含的信息还是不一样的。
![image.png](https://s2.ax1x.com/2019/12/02/QnLRmt.png)
***
#2.Kafka的安装
## 2.1 ZK 的安装
配置简单的心跳超时时间还有日志的存放路径。
```java
# ZooKeeper服务器心跳时间，单位为ms
tickTime=2000
# 投票选举新leader的初始化时间
initLimit=10
# leader与follower心跳检测最大容忍时间，响应超过syncLimit*tickTime，leader认为
# follower“死掉”，从服务器列表中删除follower
syncLimit=5
# 数据目录
dataDir=/tmp/zookeeper/data
# 日志目录
dataLogDir=/tmp/zookeeper/log
# ZooKeeper对外服务端口
clientPort=2181
```
## 2.2 Kafka的安装
配置Kafka的broker id,还有暴露的协议和端口，还有zk的链接。
```java
# broker的编号，如果集群中有多个broker，则每个broker的编号需要设置的不同
broker.id=0
# broker对外提供的服务入口地址
listeners=PLAINTEXT://localhost:9092
# 存放消息日志文件的地址
log.dirs=/tmp/kafka-logs
# Kafka所需的ZooKeeper集群地址，为了方便演示，我们假设Kafka和ZooKeeper都安装在本机
zookeeper.connect=localhost:2181/kafka
```
# 2.3 启动Kafka
```java
bin/kafka-server-start.sh –daemon config/server.properties #后台启动
```
# 3.Kafka的一些基本工具使用
## 3.1创建Topic
```java
 bin/kafka-topics.sh --zookeeper localhost:2181/kafka --create --topic topic-demo --replication-factor 3 --partitions 4
 #bin/kafka-topics.sh --zookeeper ${注册到zk的url} --create --topic ${topic-name} --relication-factor 副本因子 --partitions 分区数
```
## 3.2查看分区信息
```java
bin/kafka-topics.sh --zookeeper localhost:2181/kafka --describe --topic topic-demo
#bin/kafka-topics.sh --zookeeper ${注册到zk的url} --describe --topic {topic-name}
```
## 3.3 查看所有分区信息
```java
bin/kafka-topic.sh --list --zookeeper localhost:2181/kafka
```
## 3.4 生产者生产消息
```java
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic topic-demo
#bin/kafka-console-producer.sh --broker-list ${kafka部署暴露的地址} --topic {topic-name}
```
## 3.5 消费者消费消息
```java
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic topic-demo
#bin/kafka-console-consumer.sh --bootstrap-server ${kafka部署暴露的地址}--topic {topic-name}
```
