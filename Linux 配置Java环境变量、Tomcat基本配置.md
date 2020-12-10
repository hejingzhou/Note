有一天不写了说明又放弃了

#### Linux 配置Java环境变量 

1、找到``` /etc/profile ```
2、进行在尾部添加
```
JAVA_HOME=/home/software/jdk1.8.0_271
PATH=$JAVA_HOME/bin:$PATH
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:
$JAVA_HOME/lib/tools.jar
export JAVA_HOME
export PATH
```
#### 解压命令  
```tar -zxvf xxx.tar.gz```  
#### Windows 向Linux传文件  
``` scp test.txt root@服务器ip:/home/software```

如果报错

```bash
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ED25519 key sent by the remote host is
SHA256:O2NKMTBPHZOfwaYAjVFfAgxPNH+KBx2nMtRuB11dowU.
Please contact your system administrator.
Add correct host key in /Users/jingzhouhe/.ssh/known_hosts to get rid of this message.
Offending ED25519 key in /Users/jingzhouhe/.ssh/known_hosts:1
ED25519 host key for 39.100.87.24 has changed and you have requested strict checking.
Host key verification failed.
lost connection
```

那么就是清空报错内容中显示路径的know_hosts的内容。

#### 启动与关闭Tomcat  

1、开启```./startup.sh```
2、关闭```./shutdown.sh```  
3、浏览器访问 ip:8080测试是否搭建成功

#### 实时查看Log

`tail -f catalina.out`  catalina.out文件 位置在主目录/logs/下

#### 进入Tomcat Web应用程序管理者  
1、进入/config/tomcat-users.xml 配置访问账户密码
```html
<role rolename="manager-gui"/>
<user username="he" password="hejingzhou" roles="manager-gui"/>
```
2、配置外网可进入，进行修改/apache-tomcat-10.0.0-M9/webapps/manager/META-INF/context.xml文件，进行修改可访问IP正则

```html
<Context antiResourceLocking="false" privileged="true" >
  <CookieProcessor className="org.apache.tomcat.util.http.Rfc6265CookieProcessor"
                   sameSiteCookies="strict" />
  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1|\d+\.\d+\.\d+\.\d+" />
  <Manager sessionAttributeValueClassNameFilter="java\.lang\.(?:Boolean|Integer|Long|Number|String)|org\.apache\.catalina\.filters\.CsrfPreventionFilter\$LruCache(?:\$1)?|java\.util\.(?:Linked)?HashMap"/>
</Context>
```
3、浏览器访问 ip:8080点击右侧Manager App 输入账户密码即可进行管理Tomcat
![web_app_manager.png](https://upload-images.jianshu.io/upload_images/4329671-b8010b871cb99855.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

#### 去掉8080端口访问，采用默认80端口访问(访问Tomcat不用刻意加:8080了)  
1、找到并打开apache-tomcat-10.0.0-M9/conf/server.xml
```html
<Connector port="8080" protocol="HTTP/1.1"
           connectionTimeout="20000"
           redirectPort="8443" />
```
2、将端口号改为80即可  
3、重启Tomcat