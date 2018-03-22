# spring-aop的配置类方式配置切面

## 使用@EnableAspectJAutoProxy注解启用aspectJ的注解以及表达式支持
```
@Configuration  //此注解声明当前类为一个Java配置类
@EnableAspectJAutoProxy  //启用aspectJ的注解以及表达式支持
public class AppConfig {


    /**
     * 配置UserService,方法名对应Bean的Id
     * @return
     */
    @Bean
    public UserService userService(){
        return new UserService();
    }

    @Bean
    public UserServiceAspect serviceAspect(){
        return new UserServiceAspect();
    }


}
```