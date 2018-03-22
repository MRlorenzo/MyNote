# 使用配置类配置Spring

### 1.创建配置类

```
/**
 * Create by lorenzo on 18-1-25.
 * 这是一个Java配置类，它的作用和XML配置文件是等价的
 * Java配置类必须使用@Configuration注解标识
 */
@Configuration
public class AppConfig {

    /**
     * 定义一个方法，方法名就作为Bean在容器中的标识
     * 并且这个方法需要使用@Bean注解表示，类似于XML中的<bean></bean>
     * 这个注解也可以指定initMethod和destroyMethod
     * @return
     */
    @Bean(initMethod = "init",destroyMethod = "destroy")
    @Scope("prototype")
    public UserService userService(){
        return new UserService();
    }

    @Bean
    public UserDao userDao(){
        return new UserDao();
    }
}

```

### 2.创建注解解析工厂

```
//创建注解上下文工厂，在构造方法传入的是一个配置类的class对象
ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
UserService service = context.getBean("userService",UserService.class);
service.add();
```

