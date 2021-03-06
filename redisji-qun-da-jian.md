# CentOS 7下搭建Redis集群

## 1、安装Redis

使用yum安装redis：

`yum install redis`

使用yum安装redis-trib，用来管理集群：

`yum install redis-trib`

## 2、配置Redis实例

在目录下创建实例目录：

`cd /var/lib/redis/`

`mkdir 7000 7001 7002`

为每个实例设置配置文件：

`cd 7000/`

`vim redis.conf`

在文件中增加如下配置：

`bind 172.31.144.106`

`port 7000`

`cluster-enabled yes`

`cluster-config-file nodes.conf`

`cluster-node-timeout 5000`

`appendonly yes`

`daemonize yes`

按照上面的方法，分别为7001和7002实例设置配置文件。

拷贝redis运行脚本：

`cp /usr/bin/redis-server ./`

`cp /usr/bin/redis-trib ./`

## 3、启动实例

启动7000实例：

`cd 7000`

`../redis-server ./redis.conf`

按照上面的方法，分别启动7001、7002实例。

按照3&4上面的方法，启动第2台机器上的7000实例、7001实例、7002实例。

## 4、创建集群

`./redis-trib create --replicas 1 172.31.144.105:7000 172.31.144.105:7001 172.31.144.105:7002 172.31.144.106:7000 172.31.144.106:7001 172.31.144.106:7002`

## 5、测试集群

连接redis集群，测试：

`redis-cli -c -h 172.31.144.105 -p 7000`

`172.31.144.105:7000> set foo bar`

`-> Redirected to slot [12182] located at 172.31.144.105:7001`

`OK`

`172.31.144.105:7001> set hello world`

`-> Redirected to slot [866] located at 172.31.144.105:7000`

`OK`

`172.31.144.105:7000> get foo`

`-> Redirected to slot [12182] located at 172.31.144.105:7001`

`"bar"`

`172.31.144.105:7001> get hello`

`-> Redirected to slot [866] located at 172.31.144.105:7000`

`"world"`

`172.31.144.105:7000> `



