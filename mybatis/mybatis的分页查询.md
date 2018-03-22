# mybatis的分页方法

## 使用RowBounds对象进行分页查询
(这种方式是不推荐使用的，因为是基于逻辑分页，当有大量数据的时候由于会先将所有的记录查询出来在执行分页， 因此效率极低)
```
		@Select("SELECT * FROM USER_INFO")
		@ResultMap("edu.nf.ch11.dao.UserDao.userMap")
		List<Users> findUsers(RowBounds rowBounds);
```
使用方法：
		创建RowBounds对象，
		第一个参数表示从第几条开始查询
        第二个参数是取多少条记录
```
        RowBounds rowBounds = new RowBounds(2 , 2);
        UserDao userDao = new UserDaoImpl();

        userDao.findUsers(rowBounds)
			.forEach((u)-> System.out.println(u.getUserName()));
```

## 使用PageHelper插件执行物理分页查询(推荐)
```
		@Select("SELECT * FROM USER_INFO")
		@ResultMap("edu.nf.ch11.dao.UserDao.userMap")
		List<Users> findUsers2();
```
使用方法：
### 第一步：添加PageHelper依赖
```
        <dependency>
            <groupId>com.github.pagehelper</groupId>
            <artifactId>pagehelper</artifactId>
            <version>5.1.2</version>
        </dependency>
```
### 配置mybatis核心配置文件
在mybatis核心配置文件中的typeAliases节点后添加内容
```
    <plugins>
        <!-- 配置分页的拦截器 -->
        <plugin interceptor="com.github.pagehelper.PageInterceptor">
            <!-- 指定数据库方言 -->
            <property name="helperDialect" value="mysql"/>
        </plugin>
    </plugins>
```
效果如下
```
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>

    <typeAliases>
        <package name="edu.nf.ch11.entity"/>
    </typeAliases>

    <plugins>
        <!-- 配置分页的拦截器 -->
        <plugin interceptor="com.github.pagehelper.PageInterceptor">
            <!-- 指定数据库方言 -->
            <property name="helperDialect" value="mysql"/>
        </plugin>
    </plugins>

    <environments default="mysql">
        <environment id="mysql">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/test?useUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <mapper resource="mapper/UserMapper.xml"/>
    </mappers>

</configuration>
```
通过PageHelper的setPage静态方法设置参数
```
        UserDao userDao = new UserDaoImpl();
		
        //通过PageHelper的setPage静态方法设置参数
        PageHelper.startPage(2,2);
		
        //执行dao查询
        List<Users> list = userDao.findUsers2();
		
        //创建PageInfo对象封装list
        PageInfo<Users> page = new PageInfo<>(list);

        System.out.println("当前页："+page.getPageNum());
        System.out.println("首页："+page.getNavigateFirstPage());
        System.out.println("上一页："+page.getPrePage());
        System.out.println("下一页："+page.getNextPage());
        System.out.println("总页数："+page.getPages());
        System.out.println("尾页："+page.getNavigateLastPage());

        list.forEach((u)-> System.out.println(u.getUserName()));
```