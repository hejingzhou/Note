有一天不写了说明又放弃了

#### Lombok基本使用

1、添加maven

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.16</version>
    <scope>provided</scope>
</dependency>
```

2、设置IDE启动注解处理

![enable_annotation](/Users/jingzhouhe/Desktop/学习文档/lom.png)

3、在实体类上进行注解

如果该类所有属性设置get-set方法则在类名上添加```@Data```

如果在该类添加重写toString():String方法在类名上添加```@ToString(callSuper = true)```

如果指定某个属性设置get-set方法则在该属性上添加```@Getter@Setter```

#### JWT使用

1、依赖maven

```
<!--    JWT    -->
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>0.11.2</version>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-impl</artifactId>
    <version>0.11.2</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-jackson</artifactId>
    <version>0.11.2</version>
    <scope>runtime</scope>
</dependency>
```

2、创建生成JWT进行下发

```java
@RequestMapping(path = "/userLogin", method = RequestMethod.GET)
public BaseResponse<UserInfoBean> userLogin(@RequestParam(name = "userName", required = false) String userName,
                                            @RequestParam(value = "password", required = false) String password) {
    if (TextUtils.isEmpty(userName) || TextUtils.isEmpty(password)) {
        mBaseResponseData.setCode("-1");
        mBaseResponseData.setMessage("缺少参数" + (StringUtils.isEmpty(userName) ? "userName" : "password"));
        return mBaseResponseData;
    }
    //组装用户关键数据
    UserInfoBean userInfoBean = new UserInfoBean();
    userInfoBean.setUserId("1");
    userInfoBean.setMobile("15588642636");
    userInfoBean.setUserName(userName);
    //登录时创建Token
    //有效时长5分钟
    //Map<String,String> claimsMap = new HashMap<>();
    //claimsMap.put("权限","Admin");
    long dateCurr = System.currentTimeMillis();
    Date expiresDate = new Date(dateCurr + 1000 * 60 * 5);
    String jws = Jwts.builder()
            .setSubject(mGson.toJson(userInfoBean))
            .setIssuedAt(new Date(dateCurr))
            .setExpiration(expiresDate)
            //.setClaims(claimsMap)
            .signWith(mKey)
            .compact();
    userInfoBean.setToken(jws);
    mBaseResponseData.setData(userInfoBean);
    return mBaseResponseData;
}
```

3、标准jwt，各个参数的含义

```json
iss: 签发者
sub: 面向用户
aud: 接收者
iat(issued at): 签发时间
exp(expires): 过期时间
nbf(not before)：不能被接收处理时间，在此之前不能被接收处理
jti：JWT ID为web token提供唯一标识
```

4、需要注意个东西

```setSubject()```方法不能和```setPayload```不能同时存在。可以把所有数据放到```setPayload()```里面,因为```setPayload()```里面可以设置所有属性比如：

 ```json
{"sub":"userInfo","aud":"sina.com","iss":"baidu.com","iat":1528360628,"nbf":1528360631,"jti":"253e6s5e","exp":1528360637} 
 ```

否则就使用每个方法单独设置

#### 热更新（解决调试阶段需要多次运行项目问题）

1、修改maven

```xml
<!-- 热更新 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <version>2.4.0</version>
</dependency>
```

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <!--       添加该标签启动热更新         -->
            <configuration>
                <fork>true</fork>
            </configuration>
        </plugin>
    </plugins>
</build>
```

2、配置IDE

配置一：

![](/Users/jingzhouhe/Desktop/学习文档/hot_run.png)

配置二：

![](/Users/jingzhouhe/Desktop/学习文档/hot_run_2.png)

