# CentOS 7下配置https代理

1. ## 服务器配置
2. ### 安装openssl
3. 使用yum安装openssl：

`yum install -y gcc openssl openssl-devel`

`cd /etc/squid/`

* 生成加密代理证书：

`openssl req -new > testagentserver.csr`

`openssl rsa -in privkey.pem -out testagentserver.key`

`openssl x509 -in testagentserver.csr -out testagentserver.crt -req -signkey testagentserver.key -days 3650`

1. ### 配置squid

修改squid.conf配置文件

`vim squid.conf`

`http_access allow all #deny修改为allow`

`https_port 443 cert=/etc/squid/testagentserver.crt key=/etc/squid/testagentserver.key`

`cache_dir ufs /var/spool/squid 100 16 256     #打开这个注释，保证/var/spool/squid这个缓存目录存在`

* 重新启动squid服务

`service squid stop`

`service squid status`

`service squid start`

`service squid status`

1. ## 客户端配置

安装openssl：

`yum install -y gcc openssl openssl-devel`

安装stunnel，实现客户端代理：

`yum install stunnel`

`cd /etc/stunnel/`

配置stunnel

`vim stunnel.conf`

`client = yes`

`[https]`

`accept = 127.0.0.1:4443`

`connect = 172.31.144.101:443`

启动stunnel：

`/usr/bin/stunnel /etc/stunnel/stunnel.conf`

验证查看stunnel启动状态：

`ps -ef|grep stunnel`

配置环境变量：

`vim /etc/profile`

`https_proxy=http://127.0.0.1:4443`

`export https_proxy`

`source /etc/profile`

测试：

`curl https://www.baidu.com`

如果访问失败，请根据错误提示修改。

