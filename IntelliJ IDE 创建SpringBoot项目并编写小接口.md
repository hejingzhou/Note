有一天不写了说明又放弃了

#### 最新IntelliJ IDE 没法创建Spring Boot项目 

1、按照以前需要安装Spring Boot插件但是现在这个插件也没了

2、安装Spring Assistant插件，重启Ide.

#### 具体创建过程

[这位小哥哥写的很详细](https://www.jianshu.com/p/28e1aedb4819)

#### 创建完后springframework各种类报红找不到

1、创建Maven的setting.xml文件（这一步部和下一步不是必须 只是为了更快下载）

![create_maven_setting.xml.png](https://github.com/hejingzhou/Note/blob/master/imageResource/create_setting.xml.png)  

我创建过所以是open Settings.xml

2、然后打开settings.xml更换下载源（这一步部不是必须 只是为了更快下载）

```xml
<mirrors>
    <mirror>
        <id>alimaven</id>
        <name>aliyun maven</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
        <mirrorOf>central</mirrorOf>
     </mirror>
</mirrors>
```

3、缺失类肯定是Maven引入类没引入进来，故检查maven文件，发现这个控件初始化的时候引入SpringBoot的依赖貌似缺版本号

去[Maven仓库网上去搜spring-boot-starter-web](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web) 找到最新的 现在为2.4.0版本拷贝依赖

```xml
<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <version>2.4.0</version>
</dependency>
```

4、删除它初始化的时候引入的spring-boot-starter-web，其实就是它初始化的时候没有版本号这一行指定，然后Rebuild下就好了

#### 运行

![Run](/Users/jingzhouhe/Desktop/学习文档/run.png)

#### 启动瞬间就Tomcat就又挂了没任何报错

1、查看maven文件

```xml
<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-tomcat</artifactId>
			<scope>scope</scope>
</dependency>
```

修改为

```xml
<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-tomcat</artifactId>
			<scope>compile</scope>
</dependency>
```

为什么？我查的资料我理解的意思大概也就相当与Java注解的运行时参数，指定这个Tomcat什么时候该处理什么时候不用处理，分为以下几种情况

- compile:默认值，表示当前依赖包，要参与当前项目的编译，后续测试，运行时，打包
- provided:代表在编译和测试的时候用，运行，打包的时候不会打包进去
- test：表示当前依赖包只参与测试时的工作：比如Junit
- runtime：表示当前依赖包只参与运行周期，其他跳过了
- system：从参与度和provided一致，不过被依赖项不会从maven远程仓库下载，而是从本地的系统拿。需要
- systemPath属性来定义路径  

比如我的Tomcat在服务器上有部署那么我就可以在打包的时候不打包进去，如果没有，我可以把Tomcat打包进来发布，如果没报错就完事了。

```  bash
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::                (v2.4.0)

2020-11-19 16:33:43.840  INFO 2333 --- [           main] com.tcoy.mall.MallApplication            : Starting MallApplication using Java 15.0.1 on JingZhoudeMacBook-Pro.local with PID 2333 (/Users/jingzhouhe/Desktop/mall/target/classes started by jingzhouhe in /Users/jingzhouhe/Desktop/mall)
2020-11-19 16:33:43.859  INFO 2333 --- [           main] com.tcoy.mall.MallApplication            : No active profile set, falling back to default profiles: default
2020-11-19 16:33:45.247  INFO 2333 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2020-11-19 16:33:45.266  INFO 2333 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2020-11-19 16:33:45.267  INFO 2333 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.39]
2020-11-19 16:33:45.416  INFO 2333 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2020-11-19 16:33:45.417  INFO 2333 --- [           main] w.s.c.ServletWebServerApplicationContext : Root WebApplicationContext: initialization completed in 1436 ms
2020-11-19 16:33:45.667  INFO 2333 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
2020-11-19 16:33:45.957  INFO 2333 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2020-11-19 16:33:45.990  INFO 2333 --- [           main] com.tcoy.mall.MallApplication            : Started MallApplication in 2.798 seconds (JVM running for 3.523)
```

如果这样结果就说明跑起来了

#### 如果然后又报错，并且```ServletInitializer```这个类报错

```ServletInitializer```这个类是通过Spring Assistant插件创建项目的时候自动生成的。查的资料需要在maven中添加javaee-api依赖

```xml
<dependency>
   <groupId>javax</groupId>
   <artifactId>javaee-api</artifactId>
   <version>8.0.1</version>
</dependency>
```

reload Maven 然后 ReBuild项目后就彻底没问题了。

#### 编写一个小接口

1、创建一个Controller类

```java
package com.tcoy.mall.controller;

import com.tcoy.mall.entity.UserEntity;
import org.springframework.web.bind.annotation.*;

import java.util.Calendar;

@RestController
public class TestController {

    /**
     * 获取请求回应信息
     *
     * @param name 名字
     * @return SoringBoot回应的数据
     */
    @RequestMapping(path = "/hello")
    public String helloSpringBoot(@RequestParam(name = "name") String name) {
        return "Hello " + name + " Use SpringBoot Return";
    }

    /**
     * 获取用户请求信息
     *
     * @param name 用户名字
     * @return 用户信息
     */
    @RequestMapping(path = "/queryUser")
    @ResponseBody
    public UserEntity queryUser(@RequestParam(name = "name") String name, @RequestHeader("User-Agent") String userAgent) {
        UserEntity userInfo = new UserEntity();
        Calendar c = Calendar.getInstance();
        int year = c.get(Calendar.YEAR);
        int month = c.get(Calendar.MONTH) + 1;
        int date = c.get(Calendar.DATE);
        int hour = c.get(Calendar.HOUR_OF_DAY);
        int minute = c.get(Calendar.MINUTE);
        int second = c.get(Calendar.SECOND);
        userInfo.setDate(year + "-" + month + "-" + date + " " + hour + ":" + minute + ":" + second);
        userInfo.setName(name);
        userInfo.setDevelopTools("IntelliJ IDE");
        userInfo.setUserAgent(userAgent);
        return userInfo;
    }

}
```

2、浏览器请求接口

- http://localhost:8080/hello?name=HeJingzhou

Hello HeJingzhou Use SpringBoot Return

* http://localhost:8080/queryUser?name=HeJingzhou

```json
{"name":"HeJingzhou","developTools":"IntelliJ IDE","date":"2020-11-19 16:54:24","userAgent":"Mozilla/5.0 (Macintosh; Intel Mac OS X 11_0_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.198 Safari/537.36"}
```

#### 附加：UserEntity.java

```java
package com.tcoy.mall.entity;

public class UserEntity {
    private String name;
    private String developTools;
    private String date;
    private String userAgent;
		***
  	get():String
		set():String
    toString:String
}

```

