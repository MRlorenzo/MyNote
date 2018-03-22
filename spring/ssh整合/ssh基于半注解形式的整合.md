# ssh的半注解整合

## 第一步：添加依赖
Spring核心依赖：
```
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-context</artifactId>
	<version>4.3.14.RELEASE</version>
</dependency>
```
整合JPA（实现方为hibernate）所需依赖
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
整合struts所需依赖
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
<context:component-scan base-package="edu.nf.ch17" />
```
配置数据源
```
<bean id="dataSource" class="org.apache.commons.dbcp2.BasicDataSource" destroy-method="close">
	<!--连接属性-->
	<property name="driverClassName" value="com.mysql.jdbc.Driver"/>
	<property name="url" value="jdbc:mysql://localhost:3306/test?useUnicode=true&amp;characterEncoding=UTF-8"/>
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
	<property name="packagesToScan" value="edu.nf.ch17.entity"/>
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
        <param-value>classpath:applicationContext.xml</param-value>
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

## 第四步：配置struts核心配置文件
class属性不再是完整类名了，而是在Spring容器中的id
```
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE struts PUBLIC
        "-//Apache Software Foundation//DTD Struts Configuration 2.5//EN"
        "http://struts.apache.org/dtds/struts-2.5.dtd">

<struts>

    <package name="pkg" extends="json-default" strict-method-invocation="false">

        <action name="*" class="userAction" method="{1}User">
            <result type="json">
                <param name="root">vo</param>
            </result>
            <result name="input" type="json">
                <param name="root">vo</param>
            </result>
        </action>

    </package>

</struts>
```

## 注解的配置与简单案例
利用dao模式将重复的CRUD提取出来
BaseDao接口：
```
public interface BaseDao<T> {

    /**
     * 根据id查询实体
     * @param clazz
     * @param id
     * @return
     */
    T findEntityById(Class<T> clazz , Serializable id);

    /**
     * 持久化实体
     * @param entity
     */
    void persist(Object entity);

    /**
     * 更新实体
     * @param entity
     */
    void update(Object entity);

    /**
     * 删除实体
     * @param entity
     */
    void remove(Object entity);

    /**
     * 查询所有的
     * @return
     */
    List<T> findAll(Class<T> clazz);
}
```
BaseDao接口的实现类BaseDaoImpl:
```
public class BaseDaoImpl<T> implements BaseDao<T> {

    @PersistenceContext
    protected EntityManager em;

    @Override
    public T findEntityById(Class<T> clazz, Serializable id) {
        return em.find(clazz,id);
    }

    @Override
    public void persist(Object entity) {
        em.persist(entity);
    }

    @Override
    public void update(Object entity) {
        em.merge(entity);
    }

    @Override
    public void remove(Object entity) {
        em.remove(entity);
    }

    @Override
    public List<T> findAll(Class<T> clazz) {
        String jpql = "from " + clazz.getSimpleName();
        return em.createQuery(jpql , clazz).getResultList();
    }
}
```
UserDao接口：
```
public interface UserDao extends BaseDao<Users>{
    
    Users findUserByName(String userName);

}
```
UserDao接口的实现类UserDaoImpl:
使用@Repository注解免去Spring配置文件的配置
```
@Repository("userDao")
public class UserDaoImpl extends BaseDaoImpl<Users> implements UserDao{

    @Override
    public Users findUserByName(String userName) {
        String jpql = "from Users u where u.userName = ?1";
        return em.createQuery(jpql , Users.class)
                .setParameter(1 , userName).getSingleResult();
    }
}
```
UserService接口：
```
public interface UserService {

    Users findUserById(String id);

    void saveUser(Users user);
}
```
UserService接口的实现类UserServiceImpl：
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
        return userDao.findEntityById(Users.class , id);
    }

    @Override
    public void saveUser(Users user) {
        try {
            userDao.persist(user);
        } catch (Exception e) {
            throw new PersistObjectException("添加失败");
        }
    }
}
```

最后，按照流程正常使用。