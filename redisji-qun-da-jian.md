# CentOS 7下搭建Redis集群

## 1、安装Redis

使用yum安装redis：

`yum install redis`

使用yum安装redis-trib，用来管理集群：

`yum install redis-trib`

## 2、启动Redis实例

在目录下创建实例目录：

`cd /var/lib/redis/`

`mkdir 7000 7001 7002`

为每个实例设置配置文件：

`cd 7000/`

`vim redis.conf`

在文件中增加如下配置：

`port 7000`

`cluster-enabled yes`

`cluster-config-file nodes.conf`

`cluster-node-timeout 5000`

`appendonly yes`

`daemonize yes`

拷贝redis运行脚本：

`cp /usr/bin/redis-server ./`

`cp /usr/bin/redis-trib ./`

按照上面的方法，分别为7001和7002实例设置配置文件。

