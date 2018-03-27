# Bean的生命周期

### Bean的初始化方法以及销毁方法
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- init-method是构建Bean之后的初始化方法，destroy-method指定销毁的方法 -->
    <bean id="hello" class="xxx" init-method="init" destroy-method="destroy"/>

</beans>
```

### Bean的初始化以及销毁
```
public static void main(String[] args) {
    ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
    //bean 被初始化了，执行初始化方法
    Hello hello = context.getBean("hello",Hello.class);
    hello.say();

    //工厂销毁时一并将Bean销毁，执行销毁方法
    ((ClassPathXmlApplicationContext)context).close();
}
```
