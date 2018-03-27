# ssh整合的简单案例

## 第一步：添加依赖
Spring核心依赖：
```
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-context</artifactId>
	<version>4.3.14.RELEASE</version>
</dependency>
```
整合HIbernate所需依赖：
```
<!--Srping orm -->
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-orm</artifactId>
	<version>4.3.14.RELEASE</version>
</dependency>

<!-- hibernate-->
<dependency>
	<groupId>org.hibernate</groupId>
	<artifactId>hibernate-core</artifactId>
	<version>5.2.12.Final</version>
</dependency>

<!--Srping tx-->
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-tx</artifactId>
	<version>4.3.14.RELEASE</version>
</dependency>

<!--DBCP ConneconPool-->
<dependency>
	<groupId>org.apache.commons</groupId>
	<artifactId>commons-dbcp2</artifactId>
	<version>2.1.1</version>
</dependency>

<!-- mysql driver-->
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
	<version>5.1.44</version>
</dependency>

<dependency>
	<groupId>org.aspectj</groupId>
	<artifactId>aspectjweaver</artifactId>
	<version>1.8.13</version>
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
## 第二步：配置实体映射文件
在resource/mapping目录下创建 实体.hbm.xml文件，例如：
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-mapping PUBLIC "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd" >
<hibernate-mapping>

    <class name="edu.nf.ch16.entity.Users" table="USER_INFO">
        <id name="uid" type="string" column="U_ID">
            <!--主键生成策略，使用UUID-->
            <generator class="uuid"/>
        </id>

        <property name="tel" type="string" column="TEL"/>
        <property name="userName" type="string" column="USER_NAME"/>
    </class>

</hibernate-mapping>
```

## 第三步：配置Spring核心配置文件
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
配置数据源
```
    <bean id="dataSource" class="org.apache.commons.dbcp2.BasicDataSource" destroy-method="close">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/xxx?useUnicode=true&amp;characterEncoding=UTF-8"/>
        <property name="username" value="root"/>
        <property name="password" value="root"/>
        <property name="initialSize" value="5"/>
        <property name="maxTotal" value="50"/>
        <property name="maxIdle" value="20"/>
        <property name="minIdle" value="5"/>
        <property name="maxWaitMillis" value="2000"/>
    </bean>
```
整合Hibernate的SessionFactory
```
    <bean id="sessionFactory" class="org.springframework.orm.hibernate5.LocalSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <property name="hibernateProperties">
            <props>
                <prop key="hibernate.dialect">org.hibernate.dialect.MySQL55Dialect</prop>
                <prop key="hibernate.show_sql">true</prop>
                <prop key="hibernate.format_sql">true</prop>
                <prop key="hibernate.hbm2ddl.auto">update</prop>
            </props>
        </property>
        <property name="mappingLocations" value="classpath:/mapping/*.hbm.xml"/>
    </bean>
```
配置事务管理器,不同的持久层框架都有相应的事务管理器
```
    <bean id="txManager" class="org.springframework.orm.hibernate5.HibernateTransactionManager">
        <property name="sessionFactory" ref="sessionFactory"/>
    </bean>
	<!-- 配置事务通知,transaction-manager属性引用上面的事务管理器的id -->
    <tx:advice id="txAdvice" transaction-manager="txManager">
        <tx:attributes>         
            <tx:method name="*" propagation="REQUIRED" rollback-for="RuntimeException"/>
        </tx:attributes>
    </tx:advice>

    <!-- 通过AOP来配置事务的切面以及切入点和通知 -->
    <aop:config>
        <aop:pointcut id="txPointcut" expression="execution(* edu.nf.ch16.service.*.*(..))"/>
        <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointcut"/>
    </aop:config>
```
配置实体
```
    <!-- 配置UsersDao -->
    <bean id="userDao" class="edu.nf.ch16.dao.impl.UsersDaoImpl">
        <!--注入sessionFactory实例-->
        <property name="sessionFactory" ref="sessionFactory"/>
    </bean>

    <!--配置UsersService-->
    <bean id="userService" class="xxx">
        <property name="usersDao" ref="userDao"/>
    </bean>
```
将Action类交给Spring管理：
```
    <!--配置userAction-->
    <bean id="userAction" class="xxx">
        <property name="usersService" ref="userService"/>
    </bean>
```

## 第四步：配置web.xml
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

## 第五步：配置Struts核心配置文件
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