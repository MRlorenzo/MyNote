# spring半注解配置

1.指定扫描的包路径

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!--开启注解配置，通常不需要配置，因为component-san会自动开启-->
    <!--<context:annotation-config/>-->
    <!--启用spring的注解扫描，并指定需要扫描的包，
    它会扫描当前包以及子包下所有的类-->
    <context:component-scan base-package="org.nf.ch04"/>

</beans>
```

2.使用@Component标记要管理的类

```
/**
 * Create by lorenzo on 18-1-25.
 * 使用@Component注解配置Bean，value的之表示Bean的id
 * 当不指定value值的时候，默认将以类名作为id，并将首字母转换为小写
 *
 */
@Component("userAction")
public class UserAction {

    public String execute(){
        System.out.println("这是控制层");
        return "success";
    }

    /**
     * 初始化方法
     */
    @PostConstruct
    public void init(){

    }

    /**
     * 销毁方法只对单例的Bean有效，对于原型的Bean是无效的
     */
    @PreDestroy
    public void destroy(){

    }
}
```

3.示例

```
ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserAction action = context.getBean("userAction",UserAction.class);
        System.out.println(action.execute());
```

