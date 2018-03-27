# spring的简单使用

### 1.使用maven构建项目并添加spring依赖
```
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>4.3.14.RELEASE</version>
</dependency>
```
### 2.编写spring配置文件
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- 配置Bean,id表示Bean在容器中的唯一标识，class指定Bean的完整类名 -->
    <bean id="hello" class="xxx"/>


    <!-- 配置Bean，name属性表示Bean在容器中的名称，但是这个名称是可以有多个的
         并且多个别名之间可以用逗号分隔，也可以使用空格等其他字符分隔
         如果只是指定了name而没有指定id，那么name属性的第一个名称就作为id-->
    <bean name="hello2,h2,he2" class="xxx"/>

</beans>
```
### 3.使用ApplicationContext创建对象
```
//创建Spring的容器工厂
ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
//从容器工厂中获取Bean实例
Hello hello = (Hello) context.getBean("hello");
hello.say();

//根据name从容器中获取Bean实例
Hello2 hello2 = (Hello2) context.getBean("h2");
hello2.say();
```
