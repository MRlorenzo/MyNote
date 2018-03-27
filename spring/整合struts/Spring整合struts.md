# spring整合Struts

## 第一步：添加依赖
Spring核心依赖：
```
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-context</artifactId>
	<version>4.3.14.RELEASE</version>
</dependency>
```
整合struts所需依赖：
```
<dependency>
	<groupId>org.apache.struts</groupId>
	<artifactId>struts2-core</artifactId>
	<version>2.5.10</version>
</dependency>
<!-- 添加struts2的json插件依赖 -->
<dependency>
	<groupId>org.apache.struts</groupId>
	<artifactId>struts2-json-plugin</artifactId>
	<version>2.5.10</version>
</dependency>

<dependency>
	<groupId>javax.servlet</groupId>
	<artifactId>javax.servlet-api</artifactId>
	<version>3.1.0</version>
	<scope>provided</scope>
</dependency>

<!--struts2整合插件-->
<dependency>
	<groupId>org.apache.struts</groupId>
	<artifactId>struts2-spring-plugin</artifactId>
	<version>2.5.10</version>

	<!-- 排除对Spring核心框架的依赖，插件所依赖的版本相对较低 -->
	<exclusions>
		<!--排除Spring-context-->
		<exclusion>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context</artifactId>
		</exclusion>
		<!--排除Spring-web-->
		<exclusion>
			<groupId>org.springframework</groupId>
			<artifactId>spring-web</artifactId>
		</exclusion>
		<!--排除beans-->
		<exclusion>
			<groupId>org.springframework</groupId>
			<artifactId>spring-beans</artifactId>
		</exclusion>
		<!--排除core-->
		<exclusion>
			<groupId>org.springframework</groupId>
			<artifactId>spring-core</artifactId>
		</exclusion>
	</exclusions>
</dependency>

<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-web</artifactId>
	<version>4.3.14.RELEASE</version>
</dependency>

```
## 第二步：配置Spring核心配置文件
在resource目录下创建applicationContext.xml文件，例如：
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/aop
       http://www.springframework.org/schema/aop/spring-aop.xsd
       http://www.springframework.org/schema/tx
       http://www.springframework.org/schema/tx/spring-tx.xsd">

</beans>
```
将Action类交给Spring管理：
```
    <!--配置userAction-->
    <bean id="userAction" class="xxx">
        <property name="usersService" ref="userService"/>
    </bean>
```
## 第三步：配置web.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">

    <!--配置Spring的监听器-->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <!-- 配置context-param,指定Spring的核心配置文件的路径 -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationConfing.xml</param-value>
    </context-param>

    <filter>
        <filter-name>dispatcher</filter-name>
        <filter-class>org.apache.struts2.dispatcher.filter.StrutsPrepareAndExecuteFilter</filter-class>
    </filter>

    <filter-mapping>
        <filter-name>dispatcher</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    
</web-app>
```

## 第四步：配置Struts核心配置文件
```
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE struts PUBLIC
        "-//Apache Software Foundation//DTD Struts Configuration 2.5//EN"
        "http://struts.apache.org/dtds/struts-2.5.dtd">

<struts>

    <package name="pkg" extends="json-default" >
        <!--class属性不再是完整类名了，而是在Spring容器中的id-->
        <action name="findUser" class="userAction" method="findUser">
            <result name="success" type="json">
                <param name="root">user</param>
            </result>
        </action>
    </package>

</struts>
```