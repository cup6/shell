# CentOS 7下安装配置Mariadb

本节主要介绍在CentOS7下安装配置Mariadb数据库的步骤。

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

5、配置MariaDB

