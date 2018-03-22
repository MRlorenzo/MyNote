# spring整合hibernate

## 第一步：添加整合依赖
Spring核心依赖
```
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-context</artifactId>
	<version>4.3.14.RELEASE</version>
</dependency>
```
整合hibernate必须添加spring-orm依赖
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

<!--Srping tx  事务管理-->
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

## 第二步：配置HIbernate的实体映射文件
在resource/mapping目录下新建 实体名.hbm.xml文件 
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


## 第三步：编写Spring核心配置文件
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
将数据源交由Spring容器管理
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
配置由Spring提供的用于整合HIbernate的SessionFactory
```
<bean id="sessionFactory" class="org.springframework.orm.hibernate5.LocalSessionFactoryBean">
	<!--LocalSessionFactoryBean有一个dataSource字段，表示需要注入一个数据源-->
	<property name="dataSource" ref="dataSource"/>
	<!--配置hibernate的属性-->
	<property name="hibernateProperties">
		<props>
			<!--指定数据库方言,这里指定为mysql数据库的方言-->
			<prop key="hibernate.dialect">org.hibernate.dialect.MySQL55Dialect</prop>
			<!--控制台显示SQL语句-->
			<prop key="hibernate.show_sql">true</prop>
			<!--格式化sql语句-->
			<prop key="hibernate.format_sql">true</prop>
			<!--自动建模-->
			<prop key="hibernate.hbm2ddl.auto">update</prop>
			<!--配置CurrentSession , 当使用了声明式事务后则不需要设置-->
			<!--<prop key="hibernate.current_session_context_class">thread</prop>-->
		</props>
	</property>
	<!--配置hibernate的实体映射文件路径,这里指定在classpath路径下-->
	<property name="mappingLocations" value="classpath:/mapping/*.hbm.xml"/>
</bean>
```
配置事务管理器
```
<!-- 配置事务管理器,不同的持久层框架都有相应的事务管理器 -->
<bean id="txManager" class="org.springframework.orm.hibernate5.HibernateTransactionManager">
	<!--  需要注入SessionFactory -->
	<property name="sessionFactory" ref="sessionFactory"/>
</bean>

<!-- 配置事务通知,transaction-manager属性引用上面的事务管理器的id -->
<tx:advice id="txAdvice" transaction-manager="txManager">
	<!-- 配置事务属性 -->
	<tx:attributes>
		<!--
			method指定哪些方法需要参与事务
			propagation指定事务的传播级别
				REQUIRED: 如果存在一个事务，则支持当前事务。如果没有事务则开启
				SUPPORTS: 如果存在一个事务，则支持当前事务。如果没有事务，则非事务的执行
				MANDATORY: 如果存在一个事务，则支持当前事务。如果没有一个活动的事务，则抛出异常。
				REQUIRES_NEW: 总是开启一个新的事务。如果一个事务已经存在，则将这个存在的事务挂起。
				NOT_SUPPORTED: 总是非事务地执行，并挂起任何存在的事务。
				NEVER: 总是非事务地执行，如果存在一个活动事务，则抛出异常

				作者：javady
				链接：https://www.jianshu.com/p/dbaf3da5163b
				來源：简书

			rollback-for指定需要什么样的异常进行事务回滚,默认是RuntimeException
		 -->
		<tx:method name="*" propagation="REQUIRED" rollback-for="RuntimeException"/>
	</tx:attributes>
</tx:advice>

<!-- 通过AOP来配置事务的切面以及切入点和通知 -->
<aop:config>
	<!-- 定义一个切入点,使用aspect表达式,切入业务层所有的类 -->
	<aop:pointcut id="txPointcut" expression="execution(* edu.nf.ch16.service.*.*(..))"/>
	<!--定义一个切面，并引用上面配置的通知-->
	<aop:advisor advice-ref="txAdvice" pointcut-ref="txPointcut"/>
</aop:config>

```
配置完事务管理器并将SessionFactory交给事务管理器管理，在将此时的SessionFactory注入到dao层
```
<!-- 配置UsersDao -->
<bean id="userDao" class="edu.nf.ch16.dao.impl.UsersDaoImpl">
	<!--注入sessionFactory实例-->
	<property name="sessionFactory" ref="sessionFactory"/>
</bean>


<!--配置UsersService-->
<bean id="userService" class="edu.nf.ch16.service.impl.UsersServiceImpl">
	<property name="usersDao" ref="userDao"/>
</bean>
```



## 代码示例：
UserDao
```
public interface UsersDao {

    /**
     * 根据id查询用户
     * @param id
     * @return
     */
    Users findUserById(String id);

}
```
BaseDao
```
public class BaseDao {

    private SessionFactory sessionFactory;

    public void setSessionFactory(SessionFactory sessionFactory) {
        this.sessionFactory = sessionFactory;
    }

    //提供一个getSession的方法
    protected Session getSession(){
        return sessionFactory.getCurrentSession();
    }
}
```
UsersDaoImpl
```
public class UsersDaoImpl extends BaseDao implements UsersDao{

    private static final Logger logger = LoggerFactory.getLogger(UsersDaoImpl.class);

    @Override
    public Users findUserById(String id) {
        //获取CurrentSession,CurrentSession要求必须开启事务，否则运行时报异常
        //并且CurrentSession是不需要手动关闭的
        Session session = getSession();
        return session.get(Users.class , id);
    }
}
```
UsersService
```

public interface UsersService {

    Users findUsersById(String id);

}
```
UsersServiceImpl
```
public class UsersServiceImpl implements UsersService{

    private static final Logger logger = LoggerFactory.getLogger(UsersServiceImpl.class);

    private UsersDao usersDao;

    public void setUsersDao(UsersDao usersDao) {
        this.usersDao = usersDao;
    }

    @Override
    public Users findUsersById(String id) {

        return usersDao.findUserById(id);

    }
}
```
注意：在UsersServiceImpl中的所有方法都不需要手动写事务代码，由Spring以切面的形式自动完成。
```
	<!-- 通过AOP来配置事务的切面以及切入点和通知 -->
    <aop:config>
        <!-- 定义一个切入点,使用aspect表达式,切入业务层所有的类 -->
        <aop:pointcut id="txPointcut" expression="execution(* edu.nf.ch16.service.*.*(..))"/>
        <!--定义一个切面，并引用上面配置的通知-->
        <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointcut"/>
    </aop:config>
```