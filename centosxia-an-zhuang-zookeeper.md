# CentOS下安装zookeeper

**1、软件环境**

（3台服务器）

172.31.144.104 server1

172.31.144.105 server2

172.31.144.106 server3

1、Linux服务器一台、三台、五台、（2\*n+1），Zookeeper集群的工作是超过半数才能对外提供服务，3台中超过两台超过半数，允许1台挂掉 ，是否可以用偶数，其实没必要。如果有四台那么挂掉一台还剩下三台服务器，如果在挂掉一个就不行了，这里记住是超过半数。

2、Java jdk1.8 zookeeper是用java写的所以他的需要JAVA环境，java是运行在java虚拟机上的

3、Zookeeper的稳定版本Zookeeper 3.4.9版本

**2、配置&安装Zookeeper**

下面的操作是：3台服务器统一操作

1、安装Java

```
yum list java*

yum -y install java-1.8.0-openjdk*
```

2、下载Zookeeper

首先要注意在生产环境中目录结构要定义好，防止在项目过多的时候找不到所需的项目

```
#
我的目录统一放在/opt下面

#
首先创建Zookeeper项目目录

mkdir zookeeper 
#
项目目录

mkdir zkdata 
#
存放快照日志

mkdir zkdatalog
#
存放事物日志
```

下载Zookeeper

[![](http://common.cnblogs.com/images/copycode.gif "复制代码")](javascript:void%280%29;)

```
#
下载软件

cd /opt/zookeeper/


wget http:
//mirrors.cnnic.cn/apache/zookeeper/zookeeper-3.4.6/zookeeper-3.4.6
.tar.gz


#
解压软件

tar -zxvf zookeeper-3.4.6.tar.gz
```

[![](http://common.cnblogs.com/images/copycode.gif "复制代码")](javascript:void%280%29;)

**3、修改配置文件**

进入到解压好的目录里面的conf目录中，查看

[![](http://common.cnblogs.com/images/copycode.gif "复制代码")](javascript:void%280%29;)

```
#
进入conf目录

/opt/zookeeper/zookeeper-3.4.6/
conf

#
查看

[root@192.168.7.107
]$ ll

-rw-rw-r--. 1 1000 1000  535 Feb 20  2014
 configuration.xsl

-rw-rw-r--. 1 1000 1000 2161 Feb 20  2014
 log4j.properties

-rw-rw-r--. 1 1000 1000  922 Feb 20  2014 zoo_sample.cfg
```

[![](http://common.cnblogs.com/images/copycode.gif "复制代码")](javascript:void%280%29;)

\#zoo\_sample.cfg  这个文件是官方给我们的zookeeper的样板文件，给他复制一份命名为zoo.cfg，zoo.cfg是官方指定的文件命名规则。

**3台服务器的配置文件**

[![](http://common.cnblogs.com/images/copycode.gif "复制代码")](javascript:void%280%29;)

```
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/opt/zookeeper/
zkdata
dataLogDir
=/opt/zookeeper/
zkdatalog

clientPort=12181
server.1=192.168.7.100:12888:13888

server.
2=192.168.7.101:12888:13888

server.
3=192.168.7.107:12888:13888

#
server.1 这个1是服务器的标识也可以是其他的数字， 表示这个是第几号服务器，用来标识服务器，这个标识要写到快照目录下面myid文件里

#
192.168.7.107为集群里的IP地址，第一个端口是master和slave之间的通信端口，默认是2888，第二个端口是leader选举的端口，集群刚启动的时候选举或者leader挂掉之后进行新的选举的端口默认是3888
```

[![](http://common.cnblogs.com/images/copycode.gif "复制代码")](javascript:void%280%29;)

**配置文件解释：**

[![](http://common.cnblogs.com/images/copycode.gif "复制代码")](javascript:void%280%29;)

```
#
tickTime：
这个时间是作为 Zookeeper 服务器之间或客户端与服务器之间维持心跳的时间间隔，也就是每个 tickTime 时间就会发送一个心跳。

#
initLimit：

这个配置项是用来配置 Zookeeper 接受客户端（这里所说的客户端不是用户连接 Zookeeper 服务器的客户端，而是 Zookeeper 服务器集群中连接到 Leader 的 Follower 服务器）初始化连接时最长能忍受多少个心跳时间间隔数。当已经超过 5个心跳的时间（也就是 tickTime）长度后 Zookeeper 服务器还没有收到客户端的返回信息，那么表明这个客户端连接失败。总的时间长度就是 5*2000=10
 秒

#
syncLimit：

这个配置项标识 Leader 与Follower 之间发送消息，请求和应答时间长度，最长不能超过多少个 tickTime 的时间长度，总的时间长度就是5*2000=
10秒

#
dataDir：
快照日志的存储路径

#
dataLogDir：
事物日志的存储路径，如果不配置这个那么事物日志会默认存储到dataDir制定的目录，这样会严重影响zk的性能，当zk吞吐量较大的时候，产生的事物日志、快照日志太多

#
clientPort：

这个端口就是客户端连接 Zookeeper 服务器的端口，Zookeeper 会监听这个端口，接受客户端的访问请求。修改他的端口改大点
```

[![](http://common.cnblogs.com/images/copycode.gif "复制代码")](javascript:void%280%29;)

创建myid文件

[![](http://common.cnblogs.com/images/copycode.gif "复制代码")](javascript:void%280%29;)

```
#
server1

echo 
"
1
"
>
 /opt/zookeeper/zkdata/
myid

#
server2

echo 
"
2
"
>
 /opt/zookeeper/zkdata/
myid

#
server3

echo 
"
3
"
>
 /opt/zookeeper/zkdata/myid
```

[![](http://common.cnblogs.com/images/copycode.gif "复制代码")](javascript:void%280%29;)

** 4、重要配置说明**

1、myid文件和server.myid  在快照目录下存放的标识本台服务器的文件，他是整个zk集群用来发现彼此的一个重要标识。

2、zoo.cfg 文件是zookeeper配置文件 在conf目录里。

3、log4j.properties文件是zk的日志输出文件 在conf目录里用java写的程序基本上有个共同点日志都用log4j，来进行管理。

![](http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)

[![](http://common.cnblogs.com/images/copycode.gif "复制代码")](javascript:void%280%29;)

```
#
 Define some default values that can be overridden by system properties

zookeeper.root.logger=INFO, CONSOLE  
#
日志级别

zookeeper.console.threshold=INFO  
#
使用下面的console来打印日志

zookeeper.log.dir=.    
#
日志打印到那里，是咱们启动zookeeper的目录 （建议设置统一的日志目录路径）

zookeeper.log.file=
zookeeper.log
zookeeper.log.threshold
=
DEBUG
zookeeper.tracelog.dir
=
.
zookeeper.tracelog.file
=
zookeeper_trace.log


#
#
 ZooKeeper Logging Configuration

#

#
 Format is "
<
default threshold
>
 (, 
<
appender
>
)+
#
 DEFAULT: console appender only

log4j.rootLogger=
${zookeeper.root.logger}


#
 Example with rolling log file

#
log4j.rootLogger=DEBUG, CONSOLE, ROLLINGFILE
#
 Example with rolling log file and tracing

#
log4j.rootLogger=TRACE, CONSOLE, ROLLINGFILE, TRACEFILE
#
#
 Log INFO level and above messages to the console

#

log4j.appender.CONSOLE=
org.apache.log4j.ConsoleAppender
log4j.appender.CONSOLE.Threshold
=
${zookeeper.console.threshold}
log4j.appender.CONSOLE.layout
=
org.apache.log4j.PatternLayout
log4j.appender.CONSOLE.layout.ConversionPattern
=%d{ISO8601} [myid:%X{myid}] - %-5p [%t:%C{1}@%L] - %m%
n



#
 Add ROLLINGFILE to rootLogger to get log file output

#
    Log DEBUG level and above messages to a log file

log4j.appender.ROLLINGFILE=
org.apache.log4j.RollingFileAppender
log4j.appender.ROLLINGFILE.Threshold
=
${zookeeper.log.threshold}
log4j.appender.ROLLINGFILE.File
=${zookeeper.log.dir}/
${zookeeper.log.file}


#
 Max log file size of 10MB

log4j.appender.ROLLINGFILE.MaxFileSize=
10MB

#
 uncomment the next line to limit number of backup files

#
log4j.appender.ROLLINGFILE.MaxBackupIndex=10

log4j.appender.ROLLINGFILE.layout
=
org.apache.log4j.PatternLayout
log4j.appender.ROLLINGFILE.layout.ConversionPattern
=%d{ISO8601} [myid:%X{myid}] - %-5p [%t:%C{1}@%L] - %m%
n



#
#
 Add TRACEFILE to rootLogger to get log file output

#
    Log DEBUG level and above messages to a log file

log4j.appender.TRACEFILE=
org.apache.log4j.FileAppender
log4j.appender.TRACEFILE.Threshold
=
TRACE
log4j.appender.TRACEFILE.File
=${zookeeper.tracelog.dir}/
${zookeeper.tracelog.file}

log4j.appender.TRACEFILE.layout
=
org.apache.log4j.PatternLayout

#
## Notice we are including log4j's NDC here (%x)

log4j.appender.TRACEFILE.layout.ConversionPattern=%d{ISO8601} [myid:%X{myid}] - %-5p [%t:%C{1}@%L][%x] - %m%n
```

[![](http://common.cnblogs.com/images/copycode.gif "复制代码")](javascript:void%280%29;)

4、zkEnv.sh和zkServer.sh文件

zkServer.sh 主的管理程序文件

zkEnv.sh 是主要配置，zookeeper集群启动时配置环境变量的文件

5、还有一个需要注意

ZooKeeper server

**will not remove old snapshots and log files**

when using the default configuration \(see autopurge below\), this is the responsibility of the operator

zookeeper不会主动的清除旧的快照和日志文件，这个是操作者的责任。

但是可以通过命令去定期的清理。

[![](http://common.cnblogs.com/images/copycode.gif "复制代码")](javascript:void%280%29;)

```
#
!/bin/bash 
#
snapshot file dir 

dataDir=/opt/zookeeper/zkdata/version-2

#
tran log dir 

dataLogDir=/opt/zookeeper/zkdatalog/version-2


#
Leave 66 files 

count=66

count
=$[$count+1
] 
ls 
-t $dataLogDir/log.* | tail -n +$count | xargs rm -
f 
ls 
-t $dataDir/snapshot.* | tail -n +$count | xargs rm -
f 


#
以上这个脚本定义了删除对应两个目录中的文件，保留最新的66个文件，可以将他写到crontab中，设置为每天凌晨2点执行一次就可以了。
#
zk log dir   del the zookeeper log

#
logDir=

#
ls -t $logDir/zookeeper.log.* | tail -n +$count | xargs rm -f
```

[![](http://common.cnblogs.com/images/copycode.gif "复制代码")](javascript:void%280%29;)

其他方法：

第二种：使用ZK的工具类PurgeTxnLog，它的实现了一种简单的历史文件清理策略，可以在这里看一下他的使用方法 [http://zookeeper.apache.org/doc/r3.4.6/zookeeperAdmin.html](http://zookeeper.apache.org/doc/r3.4.6/zookeeperAdmin.html)

第三种：对于上面这个执行，ZK自己已经写好了脚本，在bin/zkCleanup.sh中，所以直接使用这个脚本也是可以执行清理工作的。

第四种：从3.4.0开始，zookeeper提供了自动清理snapshot和事务日志的功能，通过配置 autopurge.snapRetainCount 和 autopurge.purgeInterval 这两个参数能够实现定时清理了。这两个参数都是在zoo.cfg中配置的：

**autopurge.purgeInterval**

这个参数指定了清理频率，单位是小时，需要填写一个1或更大的整数，默认是0，表示不开启自己清理功能。

**autopurge.snapRetainCount**

这个参数和上面的参数搭配使用，这个参数指定了需要保留的文件数目。默认是保留3个。

推荐使用第一种方法

，对于运维人员来说，将日志清理工作独立出来，便于统一管理也更可控。毕竟zk自带的一些工具并不怎么给力。

**5、启动服务并查看**

1、启动服务

```
#
进入到Zookeeper的bin目录下

cd /opt/zookeeper/zookeeper-3.4.6/
bin

#
启动服务（3台都需要操作）

./zkServer.sh start
```

2、检查服务状态

```
#
检查服务器状态

./zkServer.sh status
```

通过status就能看到状态：

```
./
zkServer.sh status
JMX enabled by default
Using config: 
/opt/zookeeper/zookeeper-3.4.6/bin/../conf/zoo.cfg  
#
配置文件

Mode: follower  
#
他是否为领导
```

zk集群一般只有一个leader，多个follower，主一般是相应客户端的读写请求，而从主同步数据，当主挂掉之后就会从follower里投票选举一个leader出来。

可以用“jps”查看zk的进程，这个是zk的整个工程的main

```
#
执行命令jps

20348
 Jps

4233 QuorumPeerMain
```



