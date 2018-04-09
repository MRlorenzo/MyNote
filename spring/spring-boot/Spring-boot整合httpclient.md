# Spring-boot整合httpclient
## 选择模块
web

## 添加依赖
```
		<dependency>
			<groupId>org.apache.httpcomponents</groupId>
			<artifactId>httpclient</artifactId>
		</dependency>
```
## 创建配置类
```
@Configuration
public class RestConfiguration {

    /**
     * 注入模板的构建器
     */
    @Autowired
    private RestTemplateBuilder builder;

    @Bean
    public RestTemplate restTemplate(){
        //通过builder设置连接的超时，读写超时的时间等等设置
        builder.setConnectTimeout(2000);
        builder.setReadTimeout(2000);
        return builder.build();
    }

}
```