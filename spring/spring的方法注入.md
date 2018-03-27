# spring的方法注入

### 1.创建spring的配置文件

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="wrench" class="xxx"/>

    <bean id="worker" class="xxxr">
        <!--通过set方法注入-->
        <property name="tools" ref="wrench"/>
    </bean>

</beans>
```

### 2.使用"p:"简化property的注入配置

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--注入对象的格式：p:属性名-ref = "bean的id"-->
    <bean id="worker2" class="xxx" p:tools-ref="wrench"/>

    <!--注入值的格式：p:属性名="值"-->
    <bean id="demo" class="edu.nf.ch08.InjectValueDemo" p:userName="lorenzo" p:age="22"/>

</beans>
```