# CentOS 7下安装配置Mariadb

本节主要介绍在CentOS7下安装配置Mariadb数据库的步骤。

## 一、MariaDB安装

1、在CentOS 7下使用yum命令安装mariadb数据库及其依赖：

`yum install mariadb*`

_小注：这里加\*主要是安装mariadb其他相关的包，例如客户端连接包，按照后，可在本地直接连接server_

2、启动mariadb数据库：

`systemctl start mariadb`

3、验证启动状态：

`systemctl status mariadb`

`● mariadb.service - MariaDB database server`

`Loaded: loaded (/usr/lib/systemd/system/mariadb.service; disabled; vendor preset: disabled)`

`Active: active (running) since Thu 2017-11-16 10:43:29 CST; 4s ago`

`Process: 21750 ExecStartPost=/usr/libexec/mariadb-wait-ready $MAINPID (code=exited, status=0/SUCCESS)`

`Process: 21718 ExecStartPre=/usr/libexec/mariadb-prepare-db-dir %n (code=exited, status=0/SUCCESS)`

`Main PID: 21749 (mysqld_safe)`

`CGroup: /system.slice/mariadb.service`

`├─21749 /bin/sh /usr/bin/mysqld_safe --basedir=/usr`

`└─21927 /usr/libexec/mysqld --basedir=/usr --datadir=/var/lib/mysql --plugin-dir=/usr/lib64/mysql/plugin --log-error=/var/log/mariadb/mariad...`

`Nov 16 10:43:27 izm5e4ame76454x0an58p4z systemd[1]: Starting MariaDB database server...`

`Nov 16 10:43:27 izm5e4ame76454x0an58p4z mariadb-prepare-db-dir[21718]: Database MariaDB is probably initialized in /var/lib/mysql already, nothin... done.`

`Nov 16 10:43:27 izm5e4ame76454x0an58p4z mariadb-prepare-db-dir[21718]: If this is not the case, make sure the /var/lib/mysql is empty before runn...b-dir.`

`Nov 16 10:43:27 izm5e4ame76454x0an58p4z mysqld_safe[21749]: 171116 10:43:27 mysqld_safe Logging to '/var/log/mariadb/mariadb.log'.`

`Nov 16 10:43:28 izm5e4ame76454x0an58p4z mysqld_safe[21749]: 171116 10:43:28 mysqld_safe Starting mysqld daemon with databases from /var/lib/mysql`

`Nov 16 10:43:29 izm5e4ame76454x0an58p4z systemd[1]: Started MariaDB database server.`

`Hint: Some lines were ellipsized, use -l to show in full.`

当出现类似如上提示，表示mysql已经启动成功。如果在验证过程中出现错误，请查看/var/log/mariadb/mariadb.log文件（默认日志文件），查看启动失败的原因。

4、设置服务开机自动启动

`systemctl enable mariadb`

`Created symlink from /etc/systemd/system/multi-user.target.wants/mariadb.service to usr/lib/systemd/system/mariadb.service.`

5、初始化MariaDB

```[root@izm5e4ame76454x0an58p4z conf]# mysql_secure_installation``

```NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB``

```      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!``

```In order to log into MariaDB to secure it, we'll need the current``

`password for the root user.  If you've just installed MariaDB, and`

`you haven't set the root password yet, the password will be blank,`

`so you should just press enter here.`

`Enter current password for root (enter for none):初次运行直接回车`

`Setting the root password ensures that nobody can log into the MariaDB`

\`root user without the proper authorisation.

\`

`Set root password? [Y/n]在这里输入Y`

`New password:请在此输入需要设置的root的密码`

`Re-enter new password:请在此再次输入需要设置的root的密码`

`Remove anonymous users? [Y/n]<– 是否删除匿名用户，回车`

`Disallow root login remotely? [Y/n]<–是否禁止root远程登录,回车,`

`Remove test database and access to it? [Y/n]<– 是否删除test数据库，回车`

`Reload privilege tables now? [Y/n]<– 是否重新加载权限表，回车`

6、登录验证

`mysql -uroot -p`

输入刚才设置的密码，登录，如果登录成功，表示初始化工作已经完成。

## 二、MariaDB配置字符集

1、修改my.cnf文件：

```bash
vim /etc/my.cnf
```

在\[mysqld\]标签下添加

```
init_connect=SET collation_connection = utf8_unicode_ci'
init_connect='SET NAMES utf8'
character-set-server=utf8 
collation-server=utf8_unicode_ci 
skip-character-set-client-handshake
```

2、修改/etc/my.cnf.d/client.cnf文件：

```
vim /etc/my.cnf.d/client.cnf
```

在\[client\]中添加

```
default-character-set=utf8
```

3、修改/etc/my.cnf.d/mysql-clients.cnf文件：

```
vim
 /etc/my.cnf.d/mysql-clients.cnf
```

在\[mysql\]中添加

```
default-character-set=utf8
```

4、配置完后，重启mariadb：

```
systemctl restart mariadb
```

5、进入MariaDB查看字符集

```
mysql
> show variables like "%character%";
> show variables like "%collation%";
```

字符集配置完成。

小坑1：执行命令提示：Ignoring query to other database

原因：连接时，没有使用-u参数

## 三、配置MariaDB权限

登录mysql后，创建用户并授权：

`grant all privileges on *.* to sonar@'%' identified by 'yocRLLCl';`

重新加载配置：

flush privileges;

