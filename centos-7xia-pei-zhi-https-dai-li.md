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
2. 修改squid.conf配置文件

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

## 



