## CentOS 7下Kafka集群搭建 {#autoid-0-3-0}

**1、软件环境**

（1）linux一台或多台，大于等于2

（2）已经搭建好的zookeeper集群（172.31.144.104~106）

（3）软件版本kafka\_2.12-0.11.0.1.tgz[ ](http://apache.opencas.org/kafka/0.9.0.1/kafka_2.11-0.9.0.1.tgz)

**2、创建目录并下载安装软件**

```
#创建目录

cd /opt/
mkdir kafka 
#创建项目目录
cd kafka
mkdir kafkalogs 
#创建kafka消息目录，主要存放kafka消息
#下载软件
wget  http://apache.opencas.org/kafka/0.9.0.1/kafka_2.12-0.11.0.1.tgz


#解压软件
tar -zxvf kafka_2.12-0.11.0.1.tgz
```

**3、修改配置文件**

进入到config目录

```
cd /opt/kafka/kafka_2.12-0.11.0.1/config/
```

主要关注：server.properties 这个文件即可，我们可以发现在目录下：

有很多文件，这里可以发现有Zookeeper文件，我们可以根据Kafka内带的zk集群来启动，但是建议使用独立的zk集群，修改配置文件：

```
broker.id=0  
#当前机器在集群中的唯一标识，和zookeeper的myid性质一样

port=19092 
#当前kafka对外提供服务的端口默认是9092

host.name=192.168.7.100 
#这个参数默认是关闭的，在0.8.1有个bug，DNS解析问题，失败率的问题。

num.network.threads=3 
#这个是borker进行网络处理的线程数

num.io.threads=8 
#这个是borker进行I/O处理的线程数

log.dirs=/opt/kafka/kafkalogs/ 
#消息存放的目录，这个目录可以配置为“，”逗号分割的表达式，上面的num.io.threads要大于这个目录的个数
这个目录，如果配置多个目录，新创建的topic他把消息持久化的地方是，当前以逗号分割的目录中，那个分区数最少就放那一个

socket.send.buffer.bytes=102400 
#发送缓冲区buffer大小，数据不是一下子就发送的，先回存储到缓冲区了到达一定的大小后在发送，能提高性能

socket.receive.buffer.bytes=102400 
#kafka接收缓冲区大小，当数据到达一定大小后在序列化到磁盘

socket.request.max.bytes=104857600 
#这个参数是向kafka请求消息或者向kafka发送消息的请请求的最大数，这个值不能超过java的堆栈大小

num.partitions=1 
#默认的分区数，一个topic默认1个分区数

log.retention.hours=168 
#默认消息的最大持久化时间，168小时，7天

message.max.byte=5242880  
#消息保存的最大值5M

default.replication.factor=2  
#kafka保存消息的副本数，如果一个副本失效了，另一个还可以继续提供服务

replica.fetch.max.bytes=5242880  
#取消息的最大直接数

log.segment.bytes=1073741824 
#这个参数是：因为kafka的消息是以追加的形式落地到文件，当超过这个值的时候，kafka会新起一个文件

log.retention.check.interval.ms=300000 
#每隔300000毫秒去检查上面配置的log失效时间（log.retention.hours=168 ），到目录查看是否有过期的消息如果有，删除

log.cleaner.enable=false 
#是否启用log压缩，一般不用启用，启用的话可以提高性能

zookeeper.connect=192.168.7.100:12181,192.168.7.101:12181,192.168.7.107:1218 
#设置zookeeper的连接端口
```

上面是参数的解释，实际的修改项为：

```
#broker.id=0  每台服务器的broker.id都不能相同
#hostname
host.name=192.168.7.100

#在log.retention.hours=168 下面新增下面三项
message.max.byte=5242880
default.replication.factor=2
replica.fetch.max.bytes=5242880

#设置zookeeper的连接端口
zookeeper.connect=172.31.144.104:2181,172.31.144.105:2181,172.31.144.106:2181
```

**4、启动Kafka集群并测试**

1、启动服务

```
#从后台启动Kafka集群（3台都需要启动）
cd/opt/kafka/kafka_2.11-0.9.0.1/bin 
#进入到kafka的bin目录
./kafka-server-start.sh -daemon ../config/server.properties
```

2、检查服务是否启动

```
#执行命令jps
20348
 Jps
4233
 QuorumPeerMain
18991 Kafka
```

3、创建Topic来验证是否创建成功，更多请看官方文档：[http://kafka.apache.org/documentation.html](http://kafka.apache.org/documentation.html)

```
#创建Topic
./kafka-topics.sh --create --zookeeper 172.31.144.105:12181 --replication-factor 2 --partitions 1 --topic testtopic

#解释
--replication-factor 2   #复制两份
--partitions 1 #创建1个分区
--topic #主题为shuaige
'''

在一台服务器上创建一个发布者
'''
#创建一个broker，发布者
./kafka-console-producer.sh --broker-list 172.31.144.105:19092 --topic testtopic

'''
在一台服务器上创建一个订阅者
'''
./kafka-console-consumer.sh --zookeeper localhost:12181 --topic testtopic --from-beginning
```

测试（在发布者那里发布消息看看订阅者那里是否能正常收到~）：

4、其他命令

大部分命令可以去官方文档查看

4.1、查看topic

```
./kafka-topics.sh --list --zookeeper localhost:12181
#就会显示我们创建的所有topic
```

4.2、查看topic状态

```
/kafka-topics.sh --describe --zookeeper localhost:12181 --topic testtopic
```

OKkafka集群搭建完毕

5、其他说明标注

5.1、日志说明

默认kafka的日志是保存在/opt/kafka/kafka\_2.12-0.11.0.1/logs目录下的，这里说几个需要注意的日志

```
server.log 
#kafka的运行日志

state-change.log  
#kafka他是用zookeeper来保存状态，所以他可能会进行切换，切换的日志就保存在这里

controller.log 
#kafka选择一个节点作为“controller”,当发现有节点down掉的时候它负责在游泳分区的所有节点中选择新的leader,这使得Kafka可以批量的高效的管理所有分区节点的主从关系。如果controller down掉了，活着的节点中的一个会备切换为新的controller.
```



