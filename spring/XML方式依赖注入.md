# XML方式依赖注入

### 1.创建spring配置文件

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="tools" class="edu.nf.ch07.impl.Wrench"/>

    <!--配置Worker-->
    <bean id="worker" class="edu.nf.ch07.Worker">
        <!--通过构造方法注入一个tools的实例，
        ref表示引用容器中的某个Bean的ID-->
        <constructor-arg name="tools" ref="tools"></constructor-arg>
    </bean>
	<!--注入值的方式-->
    <bean id="demo" class="edu.nf.ch07.InjectValueDemo">
        <constructor-arg name="userName" value="lorenzo"/>
        <constructor-arg name="age" value="21"/>
    </bean>


</beans>
```

### 2.示例

```
ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");

Worker worker = context.getBean("worker",Worker.class);

worker.work();

InjectValueDemo demo = context.getBean("demo",InjectValueDemo.class);
demo.test();
```

