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



