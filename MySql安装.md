[toc]

#### 安装命令

```shell
apt-get install mysql-server
apt-get install mysql-client
apt-get install libmysqlclient-dev
```

#### 设置密码(默认没有密码)

```mysql
alter user 'root'@'localhost' identified with mysql_native_password by 'Hejingzhou00';
```

```mysql
flush privileges;
```

#### 开启允许远程连接

1、配置MySql 允许远端IP访问

```mysql
use mysql;
update user set host='%' where user='root';
select host,user from user;
flush privileges;
```

2、配置MySql 访问端口HOST

```shell
/etc/mysql/mysql.conf.d/mysqld.cnf
```

将 `bind-address  = 127.0.0.1` 

改 `bind-address = 0.0.0.0`

3、重启MySql 

```shell
service mysql restart
```



