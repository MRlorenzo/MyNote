# Bean的作用域

### scope属性
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- scope表示作用域，其意思就是spring以什么样的方式来创建Bean实例
     默认scope的值是singleton，也就是单例，这样每次从容器中getBean的时候返回的都是同一个实例。
     如果需要每次返回不用的实例，那么就指定为prototype，每次getBean的时候，spring容器就会新建一个实例返回-->
    <bean id="hello" class="xxx" scope="prototype" lazy-init="true"/>

</beans>
```

### 示例
```
/*    ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");

    Hello h1 = context.getBean("hello",Hello.class);
    Hello h2 = context.getBean("hello",Hello.class);

    System.out.println(h1 == h2);*/
    Hello hello = Hello.getInstance();
    Hello hello2 = Hello.getInstance();
    System.out.println(hello == hello2);
```
