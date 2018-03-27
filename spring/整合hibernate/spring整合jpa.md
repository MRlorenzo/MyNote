# spring整合JPA

## 第一步：添加依赖
Spring核心依赖
```
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-context</artifactId>
	<version>4.3.14.RELEASE</version>
</dependency>

```
整合HIbernate所需依赖(jpa的实现方为hibernate)
```
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

```

## 第二步：配置Spring核心配置文件
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
启用注解处理器并指定扫描的包路径
```
<context:component-scan base-package="xxx" />
```
配置数据源
```
<bean id="dataSource" class="org.apache.commons.dbcp2.BasicDataSource" destroy-method="close">
	<!--连接属性-->
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
配置JPA的适配器，指定JPA的提供方是谁。因为JPA的实现有多种， Spring对此提供了不同的适配器来适配这些提供方
```
<bean id="vendorAdapter" class="org.springframework.orm.jpa.vendor.HibernateJpaVendorAdapter">
	<!-- 在适配器中配置JPA的相应属性 -->
	<!-- 使用的数据库 -->
	<property name="database" value="MYSQL"/>
	<!-- 数据库方言 -->
	<property name="databasePlatform" value="org.hibernate.dialect.MySQL57Dialect"/>
	<!-- 显示sql语句 -->
	<property name="showSql" value="true"/>
	<!-- 自动执行DDL语句 -->
	<property name="generateDdl" value="true"/>
</bean>
```
配置EntityManagerFactory
```
<bean id="entityManagerFactory" class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean">
	<!-- 注入数据源 -->
	<property name="dataSource" ref="dataSource"/>
	<!-- 注入适配器 -->
	<property name="jpaVendorAdapter" ref="vendorAdapter"/>
	<!-- 实体的扫描路径 -->
	<property name="packagesToScan" value="xxx"/>
</bean>
```
配置JPA事务管理器，使用JpaTransactionManager
```
<bean id="txManager" class="org.springframework.orm.jpa.JpaTransactionManager">
	<property name="entityManagerFactory" ref="entityManagerFactory"/>
</bean>
```
启用注解配置声明式事务
```
    <tx:annotation-driven transaction-manager="txManager"/>
```

## 第三步：使用示例
使用@PersistenceContext注解注入一个EntityManager
```
@Repository("userDao")
public class UserDaoImpl implements UserDao{

    /**
     * 使用@PersistenceContext注解注入一个EntityManager
     */
    @PersistenceContext
    private EntityManager em;

    @Override
    public Users findUserById(String id) {
        return em.find(Users.class , id);
    }

    @Override
    public void saveUser(Users user) {
        em.persist(user);
    }
}
```
 使用@Transactional来配置事务，这个注解可以定义在类上也可以定义在方法上
 当定义在类上，表示当前类的所有方法将参与事务，如果定义在方法上
 则表示当前方法参与事务
 propagation属性指定事务的传播级别，和XML配置等价，默认是REQUIRED
 rollbackFor属性指定抛出什么异常进行事务回滚，默认是RuntimeException
 ```
 @Service("userService")
@Transactional
public class UserServiceImpl implements UserService{

    @Autowired
    private UserDao userDao;

    @Override
    public Users findUserById(String id) {
        return userDao.findUserById(id);
    }

    @Override
    public void saveUser(Users user) {
        userDao.saveUser(user);
    }
}
 ```