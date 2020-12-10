#### 发布SpringBoot项目(两种类型)

1、确认pom设置为打包为jar

```xml
<groupId>com.tcoy</groupId>
<artifactId>mall</artifactId>
<version>0.0.1-SNAPSHOT</version>
<!-- 分为jar模式和war两种 -->
<packaging>jar</packaging>
<name>Mall</name>
<description>Demo project for Spring Boot</description>
```

2、确定Tomcat生命周期

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-tomcat</artifactId>
    <!-- 如果为jar类型则设置为compile war设置为provided类型  让其使用外部Tomcat -->
    <scope>compile</scope> 
</dependency>
```

3、进行maven打包

<img src="/Users/jingzhouhe/Desktop/学习文档/WechatIMG50.png" align=left>

4、放置到服务器运行

jar 类型 `java -jar mall-0.0.1-SNAPSHOT.jar`

war类型 放入`Tomcat->webapps/`目录下后启动Tomcat即可

5、如果为jar 则访问http://39.100.87.24:8080/hello?name=hejing

如果为war 则访问http://39.100.87.24:8080/mall/hello?name=hejing 需要添加war包名称

#### 报错

Failed to execute goal org.apache.maven.plugins:maven-clean-plugin:3.1.0:clean (default-clean) on project mall: Failed to clean project: Failed to delete C:\Users\he\Desktop\mall\target

那么maven添加

```xml
<dependency>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.8.1</version>
</dependency>
```

	#### 如果轻轻404，而且还不报错

一定得确认下这个版本统一问题

1、pom.xml

```xml
<properties>
    <java.version>8</java.version>
</properties>
```

2、确认Java编译版本![](/Users/jingzhouhe/Desktop/学习文档/Java编译版本.png)

3、确认项目版本

![](/Users/jingzhouhe/Desktop/学习文档/项目版本.png)

4、确认Module版本

![](/Users/jingzhouhe/Desktop/学习文档/Module版本.png)

5、确认不是服务器和本地不是一个Java Sdk 一个  Open Sdk