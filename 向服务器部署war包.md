#### 配置启动类

1、启动类中，继承SpringBootServletInitializer,然后重写configure方法

```java
package com.tcoy.mall;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.builder.SpringApplicationBuilder;
import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;

@SpringBootApplication
public class MallApplication extends SpringBootServletInitializer {

	@Override
	protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
		return builder.sources(MallApplication.class);
	}

	public static void main(String[] args) {
		SpringApplication.run(MallApplication.class, args);
	}

}
```

2、pom文件修改

```xml
<groupId>com.tcoy</groupId>
<artifactId>mall</artifactId>
<version>1</version>
<packaging>war</packaging>
<name>mall</name>
<description>Demo project for Spring Boot</description>
```

packaging标签修改为war



