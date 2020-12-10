#### 获取yml 与 properties

```java
@Component
public class NeoProperties {
	@Value("${com.neo.title}")
	private String title;
	@Value("${com.neo.description}")
	private String description;
	//省略getter settet方法
}
```