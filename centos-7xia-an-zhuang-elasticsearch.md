# CentOS 7下安装ElasticSearch

## 一、配置yum

* 下载安装ES的yum公钥：

`rpm --import https://packages.elastic.co/GPG-KEY-elasticsearch`

小注：确保服务器能访问https网站，如果是内网服务器，可以使用squid配置https的方式实现代理https，请参考[《CentOS 7下配置代理服务器squid》](/centos7xia-pei-zhi-dai-li-fu-wu-qi-squid.md)。

* 配置ES的yum源：

`vim /etc/yum.repos.d/elasticsearch.repo`

```
[elasticsearch-6.x]
name=Elasticsearch repository for 6.x packages
baseurl=https://artifacts.elastic.co/packages/6.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
```

## 二、安装ElasticSearch

使用yum命令安装：

`sudo yum install elasticsearch`

按照提示完成安装。

## 三、配置ElasticSearch

修改es配置文件：

`vim /etc/elasticsearch/elasticsearch.yml`

最小的es配置如下：

`cluster.name: snoar-es`

此处指定集群的名称，同一集群的各个结点的集群名称必须相同。

启动elasticsearch：

`systemctl start elasticsearch`

验证elasticsearch是否正常启动：

`systemctl status elasticsearch`

`● elasticsearch.service - Elasticsearch`

`Loaded: loaded (/usr/lib/systemd/system/elasticsearch.service; disabled; vendor preset: disabled)`

`Active: active (running) since Thu 2017-11-16 16:44:28 CST; 1s ago`

`Docs: http://www.elastic.co`

`Main PID: 17340 (java)`

`CGroup: /system.slice/elasticsearch.service`

          └─17340 /bin/java -Xms1g -Xmx1g -XX:+UseConcMarkSweepGC -XX:CMSInitiatingOccupancyFraction=75 -XX:+UseCMSInitiatingOccupancyOnly -XX:+Always...

`Nov 16 16:44:28 izm5eg85iprsk2ujegc29gz systemd[1]: Started Elasticsearch.`

`Nov 16 16:44:28 izm5eg85iprsk2ujegc29gz systemd[1]: Starting Elasticsearch...`

出现类似如上提示，表示ElasticSearch已经正确安装。

