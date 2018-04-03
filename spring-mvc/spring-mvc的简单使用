# Spring-MVC的使用案例

## 第一步：添加依赖
spring核心依赖
```
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>4.3.14.RELEASE</version>
        </dependency>
```
Spring-MVC所需依赖
```
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>4.3.14.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.9.3</version>
        </dependency>
```
## 第二步：配置web.xml
找到WEB-INF/web.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">

</web-app>
```
配置核心控制器
```
    <!-- 核心控制器 -->
    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        
		<!-- 默认是从WEB-INF目录下查找名为[servletName]-servlet.xml文件，
            也可以通过配置init-param指定配置文件存放的目录-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:dispatcher-servlet.xml</param-value>
        </init-param>
		
    </servlet>

    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
```

## 第三步：配置Spring核心配置文件
* 如果没有在web.xml的配置的核心控制器的servlet标签中配置contextConfigLocation，
   则在WEB-INF目录下创建一个名为  [servletName]-servlet.xml的Spring核心配置文件。例如：dispatcher-servlet.xml
* 如果配置了contextConfigLocation，则需要自定义核心配置文件存放路径。
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       http://www.springframework.org/schema/mvc/spring-mvc-4.3.xsd">

</beans>
```
启用注解并扫描包 
```
    <context:component-scan base-package="xxx"/>
```
启用mvc注解驱动
	注意：
	当不配置mvc:annotation-driven的时候，DispatcherServlet会使用默认的
	DefaultAnnotationHandlerMapping和AnnotationHandlerMethodAdapter,
	在spring3.1之后应该使用RequestMappingHandlerMapping替换DefaultAnnotationHandlerMapping
	使用RequestMappingHandlerAdapter替换AnnotationHandlerMethodAdapter
	启用mvc注解驱动,这个配置会自动注册
	RequestMappingHandlerMapping和RequestMappingHandlerAdapter,
	同时还会启用JSR的注解验证支持，@RequestBody注解支持，@ResponseBody注解支持等等
```
    <mvc:annotation-driven/>
```

```
<mvc:default-servlet-handler/>
```
### 额外配置
配置JSP的视图解析器
```
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <!-- 配置jsp访问路径的前缀 -->
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <!-- 配置jsp的后缀名 -->
        <property name="suffix" value=".jsp"/>
    </bean>
```

![spring-mvc执行流程][1]


  [1]: ./images/4647845-bcf5dc835ee870fa.png "spring-mvc执行流程"
  
  2018年03月20日 11时17分21秒