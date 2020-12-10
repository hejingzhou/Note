[toc]

#### 添加maven

```xml
<!-- pom中配置Mysql 依赖 -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>

<!-- https://mvnrepository.com/artifact/org.mybatis.spring.boot/mybatis-spring-boot-starter -->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.4</version>
</dependency>
```

#### 修改application.yml

```yaml
spring:
  datasource:
    url: jdbc:mysql://xx.xxx.xx.xx:3306/mall
    username: root
    password: Hejingzhou00
    driver-class-name: com.mysql.cj.jdbc.Driver
```

#### 编写用户注册接口

##### 1、创建用户表

![](/Users/jingzhouhe/Desktop/学习文档/表结构.png)

uid为自增ID也为UserId

##### 2、注册

插入的数据为用户名、手机号、密码，编写Mybatis的Mapper映射文件：

```java
@Mapper
public interface UserMapper {

    @Insert("INSERT INTO user (user_name,mobile,password) " +
            "VALUES (#{user_name}," +
            "#{mobile}," +
            "#{password})")
    @Options(useGeneratedKeys = true, keyProperty = "uid", keyColumn = "uid")
    void insertUser(UserRegisterArgs userRegisterArgs);
}
```

注意点：

1、接口要添加@mapper注解

2、插入语句的后边值#{userRegisterArgs对象的属性要一致}

3、因为我想要插入该用户后拿到自增ID 所以添加了@Options， keyProperty指的是对象属性相对应的一般为userId，keyColumn则为对应的表对应的字段

4、获取自增ID不是在 `void insertUser(UserRegisterArgs userRegisterArgs);`语句返回而是在执行该语句之后再从userRegisterArgs对象里面取出getUid() ，不要迷惑

编写控制器：

```
@RestController
@RequestMapping(path = "/user")
public class UserController extends BaseController {
		@Autowired
    UserMapper userMapper;
    
    /**
     * 注册用户
     *
     * @param userRegisterArgs 注册参数
     * @return 用户信息
     */
    @RequestMapping(path = "/registerUser", method = RequestMethod.GET)
    @ResponseBody
    public BaseResponse<UserInfoBean> registerUser(UserRegisterArgs userRegisterArgs) {
        userMapper.insertUser(userRegisterArgs);
        logger.info("插入的自增ID\t" + userRegisterArgs.getUid());
        UserInfoBean userInfoBean = new UserInfoBean();
        userInfoBean.setUserId(String.valueOf(userRegisterArgs.getUid()));
        userInfoBean.setMobile(userRegisterArgs.getMobile());
        userInfoBean.setUserName(userRegisterArgs.getUserName());
        
        ·······
       
   }
   
}
```

访问及结果

http://localhost:8080/user/registerUser?userName=HeJingzhou&password=hejingfei1230&mobile=15588642636

```json
{
    "code":"0",
    "data":{
        "userId":"57",
        "userName":"HeJingzhou",   		   "token":"eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJ7XCJ1c2VySWRcIjpcIjU3XCIsXCJ1c2VyTmFtZVwiOlwiSGVKaW5nemhvdVwiLFwibW9iaWxlXCI6XCIxNTU4ODY0MjYzNlwifSIsImlhdCI6MTYwNjk5Njc2NiwiZXhwIjoxNjA2OTk3MDY2fQ.Ge3C2HENoCrm7OIdhQdZDSnrCgWYVsDa99yyUICfZ3w",
        "mobile":"15588642636"
    },
    "message":"Success"
}
```

##### 3、登录

同理mapper文件代码

```sql
@Result(property = "userName", column = "user_name")
@Result(property = "userId", column = "uid")
@Select("SELECT uid,user_name,mobile FROM user WHERE user_name = '${userName}' AND password='${password}'")
UserInfoBean queryUserInfo(String userName, String password);
```

注意：

@Result 注解为避免对象属性与表字段接口对应不上造成的无法将查询的数据映射到对象上，进行声明转化对象 < -- > 表字段

同理controller层文件代码

```java
@RequestMapping(path = "/userLogin", method = RequestMethod.GET)
public BaseResponse<UserInfoBean> userLogin(@RequestParam(name = "userName", required = false) String userName,
                                            @RequestParam(value = "password", required = false) String password) {
    if (TextUtils.isEmpty(userName) || TextUtils.isEmpty(password)) {
        mBaseResponseData.setCode("-1");
        mBaseResponseData.setMessage("缺少参数" + (TextUtils.isEmpty(userName) ? "userName" : "password"));
        return mBaseResponseData;
    }
    UserInfoBean userInfo = userMapper.queryUserInfo(userName, password);
    //组装用户关键数据
    //登录时创建Token
    //有效时长5分钟
    //Map<String,String> claimsMap = new HashMap<>();
    //claimsMap.put("权限","Admin");
    long dateCurr = System.currentTimeMillis();
    Date expiresDate = new Date(dateCurr + 1000 * 60 * 5);
    String jws = Jwts.builder()
            .setSubject(mGson.toJson(userInfo))
            .setIssuedAt(new Date(dateCurr))
            .setExpiration(expiresDate)
            .signWith(mKey)
            .compact();
    userInfo.setToken(jws);
    mBaseResponseData.setData(userInfo);
    return mBaseResponseData;
}
```

访问与结果

http://localhost:8080/user/userLogin?userName=HeJingzhou&password=hejingfei1230

```json
{
    "code":"0",
    "data":{
        "userId":"56",
        "userName":"HeJingzhou",
        "token":"eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJ7XCJ1c2VySWRcIjpcIjU2XCIsXCJ1c2VyTmFtZVwiOlwiSGVKaW5nemhvdVwiLFwibW9iaWxlXCI6XCIxNTU4ODY0MjYzNlwifSIsImlhdCI6MTYwNjk5NTIwMSwiZXhwIjoxNjA2OTk1NTAxfQ.n7gE_exwtg2EJ18SAl44poCLMbapi14fJsqe_oYMmxE",
        "mobile":"15588642636"
    },
    "message":"Success"
}
```

