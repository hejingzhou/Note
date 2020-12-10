#### Redis安装

1、解压redis  `tar-zxvf  redis-6.0.9.tar.gz`

2、编译 `cd redis-6.0.9` 在目录下执行`make install `

3、启动Redis 进入`scr`目录执行`./redis-server`

带配置文件启动 `redis-server ./redis.conf `

redis 后台启动`redis-server &`

#### 基本使用

1、进入Redis主机`./redis-cli`

2、设置并取值

```bash
127.0.0.1:6379> set name hejingzhou
OK
127.0.0.1:6379> get name
"hejingzhou"
127.0.0.1:6379> 
```

#### Redis允许远程访问

编辑redis_conf文件注释掉`bind 127.0.0.1 `并且将`protected-mode yes`改为`protected-mode no`

重启Redis

可以通过RedisClient客户端进行访问

#### 设置密码

编辑配置redis.conf文件内容

`requirepass Hejingzhou00`

重启Redis

* 注意 设置密码后在后续set 操作前需要进行auth操作，auth  Hejingzhou00

```bash
root@he:/home/software/redis-6.0.9# redis-cli
127.0.0.1:6379> auth Hejingzhou00
OK
127.0.0.1:6379> set time 2020-12-08
OK
127.0.0.1:6379> get time
"2020-12-08"
127.0.0.1:6379> 
```

#### 如果采用Jedis操作

```xml
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.3.0</version>
    <type>jar</type>
    <scope>compile</scope>
</dependency>
```

```java
@GetMapping(path = "/redisTest")
public String redisTest() {
    Jedis jedis = new Jedis();
    jedis.auth("Hejingzhou00");
    // 清理所有缓存数据
    jedis.flushAll();
    jedis.set("visit", DateUtil.formatDateTime(new Date()));
    return "记录成功";
}
```

#### 





