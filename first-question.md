一、安装过程

1、安装ruby

2、安装facter

3、安装puppet

二、配置过程

1、修改主机名。例如：master.test.com

`hostname`

`vi /etc/hostname`

2、

`vi /etc/resolv.conf`

中增加

`search     test.com`

3、增加host

`vi /etc/hosts`

`ip1     master.test.com`

`ip2     client.test.com`

4、重启服务

`restart master`

测试过程

1、client执行：

`puppet agent -t --server master.test.com`

2、master执行：

puppet cert -l -a

puppet cert -s -a

三、脚本编写

入口文件：manifest/site.pp

可以分模块：modules/php5

```
               modules/mysql
```

如果运行有问题，可以将缓存文件清除：

rm /var/lib/puppet/ssl/\*

之后重启

master restart

