[这篇文章写的贼好-->作者：陈琰AC   来源：简书](https://www.jianshu.com/p/eb3cf1b1734a)

#### 安装

```shell
sudo apt update
sudo apt install nginx
```

##### apt-get方式安装Nginx相关文件目录

- 主程序： `/usr/sbin/nginx`
- 存放配置文件：` /etc/nginx`
- 存放静态文件：` /usr/share/nginx`
- 存放日志： `/var/log/nginx`
- 我的配置文件位置：`/etc/nginx/conf.d/**.conf`

##### 测试

访问IP则返回Nginx欢迎页

##### 常用命令

1、检查Nginx运行状态

```shell
sudo systemctl status nginx
nginx.service - A high performance web server and a reverse proxy server
    Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
    Active: active (running) since Wed 2020-12-16 19:31:43 CST; 1min 17s ago
      Docs: man:nginx(8)
   Process: 52373 ExecStartPre=/usr/sbin/nginx -t -q -g daemon on; master_process on; (code=exited, status=0/SUCCESS)
   Process: 52389 ExecStart=/usr/sbin/nginx -g daemon on; master_process on; (code=exited, status=0/SUCCESS)
  Main PID: 52390 (nginx)
     Tasks: 3 (limit: 4431)
    Memory: 3.4M
    CGroup: /system.slice/nginx.service
            ├─52390 nginx: master process /usr/sbin/nginx -g daemon on; master_process on;
            ├─52391 nginx: worker process
            └─52392 nginx: worker process
```

如果失败可能是80端口被占用

2、检查版本号

```shell
sudo nginx -v
```

3、启动Nginx

```shell
sudo systemctl start nginx
```

4、关闭Nginx

```shell
sudo systemctl stop nginx
```

5、重启服务

```shell
sudo systemctl restart nginx
```

6、查找Nginx配置文件路径

```shell
find / -name nginx.conf
```

7、查询Nginx进程

```shell
ps -ef | grep nginx
```

#### 配置

