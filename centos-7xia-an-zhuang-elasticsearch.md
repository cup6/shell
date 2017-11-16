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

