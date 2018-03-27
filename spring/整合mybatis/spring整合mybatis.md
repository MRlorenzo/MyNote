# spring整合mybatis

## 第一步：添加依赖
Spring核心依赖
```
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context</artifactId>
			<version>4.3.14.RELEASE</version>
		</dependency>
```
整合mybatis所需依赖
```
        <!-- mybatis依赖 -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.5</version>
        </dependency>
		
        <!-- mysql驱动 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.35</version>
        </dependency>
		
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.3.1</version>
        </dependency>
		
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>4.3.14.RELEASE</version>
        </dependency>
		
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>4.3.14.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-dbcp2</artifactId>
            <version>2.1.1</version>
        </dependency>
```
可选依赖
```
		<!-- mybatis非官方分页插件 -->
        <dependency>
            <groupId>com.github.pagehelper</groupId>
            <artifactId>pagehelper</artifactId>
            <version>5.1.2</version>
        </dependency>
		
        <!--日志管理-->
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.25</version>
        </dependency>

        <!-- junit依赖 -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
		
        <!-- gson依赖 -->
        <dependency>
            <groupId>com.google.code.gson</groupId>
            <artifactId>gson</artifactId>
            <version>2.3.1</version>
        </dependency>
```

## 第二步：定义Mapper接口
```
@Repository("dao")
public interface UserDao {

    @Insert("INSERT INTO USER_INFO(U_ID,TEL,USER_NAME) VALUES(#{uid},#{tel},#{userName})")
    void saveUser(Users user);

    @Select("SELECT * FROM USER_INFO WHERE U_ID=#{id}")
    @ResultMap("edu.nf.ch12.dao.UserDao.userMap")
    Users findUserById(String uid);

    @Select("SELECT * FROM USER_INFO")
    @ResultMap("edu.nf.ch12.dao.UserDao.userMap")
    List<Users> findUserToPage();

}

```

## 第三步：配置mapper配置文件
在resource/mapper目录下新建   [接口名]Mapper.xml文件
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="edu.nf.ch12.dao.UserDao">

    <resultMap id="userMap" type="users">
        <id property="uid" column="U_ID"/>
        <result property="tel" column="TEL"/>
        <result property="userName" column="USER_NAME"/>
    </resultMap>

</mapper>
```
## 第四步：配置Spring核心配置文件
在resource目录下新建applicationContext.xml文件
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/tx
       http://www.springframework.org/schema/tx/spring-tx.xsd">

</beans>
```
启用注解并扫描指定的包
```
    <context:component-scan base-package="xxx"/>
```
配置DBCP2的数据源连接池
```
    <bean id="dataSource" class="org.apache.commons.dbcp2.BasicDataSource" destroy-method="close">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/xxx?useUnicode=true&amp;characterEncoding=UTF-8"/>
        <property name="username" value="root"/>
        <property name="password" value="root"/>

        <!--连接池启动时的初始连接数-->
        <property name="initialSize" value="5"/>
        <!--连接池的最大值，同一个时间可以从池分配的最多连接数量，０时无限制，默认值为８-->
        <property name="maxTotal" value="50"/>
        <!--最大空闲连接数-->
        <property name="maxIdle" value="20"/>
        <!--最小空闲连接数-->
        <property name="minIdle" value="5"/>
        <!--最大连接等待时间，单位：毫秒-->
        <property name="maxWaitMillis" value="2000"/>
    </bean>

```
整合sqlSessionFactory
```
    <bean id="sqlSessionFactoryBean" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!-- 注入数据源 -->
        <property name="dataSource" ref="dataSource"/>
        <!-- 指定实体类型的别名 -->
        <property name="typeAliasesPackage" value="xxx"/>
        <!-- 当配置了mapper映射配置的时候，那么这里要指定mapper的路径 -->
        <property name="mapperLocations" value="classpath:mapper/*.xml"/>

        <property name="plugins">
            <array>
                <bean class="com.github.pagehelper.PageInterceptor">
                    <property name="properties">
                        <!-- 配置拦截器的参数属性 -->
                        <value>
                            helperDialect=mysql
                        </value>
                    </property>
                </bean>
            </array>
        </property>
    </bean>

```
Mapper接口(也就是Dao接口)的扫描配置,
        扫描这些接口的目的是为了在运行时依据这些接口来使用JDK动态代理在运行时创建dao的实现类
         因此，我们就不需要为接口来编写相应的实现类
```
    <bean id="mapperScanner" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!-- 指定Dao接口所在的包 -->
        <property name="basePackage" value="xxx"/>
    </bean>
```
配置声明式事务
```
    <bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <!-- 注入数据源 -->
        <property name="dataSource" ref="dataSource"/>
    </bean>
```
启用事务注解驱动
```
	<tx:annotation-driven transaction-manager="txManager"/>
```