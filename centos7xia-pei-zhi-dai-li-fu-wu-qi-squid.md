# CentOS7下配置代理服务器squid

因公司测试环境，只有一台服务器可以访问外网，而局域网内其他服务器也需要访问外网，经过初步选型，决定使用squid代理服务软件做上网代理服务器。

## 1、软件环境

OS：CentOS 7

代理服务器：172.31.144.101

要上网的服务器：172.31.144.102

## 2、搭建代理服务器

在CentOS 7下使用yum安装squid，命令如下：

`yum install squid`

修改squid配置文件，命令如下：

`vim /etc/squid/squid.conf`


