# 第一章

### 第一步：添加依赖
```
	<!-- mybatis依赖 -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>${mybatis.version}</version>
        </dependency>
        <!-- mysql驱动 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>${mysql.version}</version>
        </dependency>
```

### 第二步：新建mybatis核心配置文件
在resource目录下新建mybatis.xml
```
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>


</configuration>
```
为实体取别名，用于mapper文件的映射
```
    <!-- 类型别名,意思就是给实体在使用的过程中起一个别名引用 -->
    <typeAliases>
        <!-- 方式一：给每一个实体定义一个别名，通过alias指定别名 -->
        <!--<typeAlias type="edu.nf.ch01.entity.Users" alias="user"/>-->
        <!-- 方式二：直接给实体所在的包来指定别名，那么别名就是实体的类名，并将首字母变为小写 -->
        <package name="edu.nf.ch01.entity"/>
    </typeAliases>
```
配置数据源
```
    <!-- environments用于配置数据源，可以同时指定多个数据源，通过default属性
        来指定默认的数据源，对应的就是environment的id属性-->
    <environments default="mysql">
        <!-- 每一个environment都是一个独立的数据源配置,id指定唯一标识 -->
        <environment id="mysql">
            <!-- 指定为使用本地事务，也就是JDBC事务 -->
            <transactionManager type="JDBC"></transactionManager>
            <!-- mybatis本身就内置一个连接池，可以直接使用 -->
            <dataSource type="POOLED">
                <!--配置连接信息-->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/test?useUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>
```
### 第三步：定义Dao接口
```
public interface UserDao {

    /**
     * 添加用户
     * @param user
     */
    void saveUser(Users user);

}
```

### 第四步：新建mapper配置文件
在resource/mapper目录下新建 Dao接口名.xml文件
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- mapper指定要映射的dao接口的完整类型名，通过namespace指定 -->
<mapper namespace="edu.nf.ch01.dao.UserDao">

    <!-- 通过insert节点来映射UserDao中的saveUser方法,
        id指定接口中的方法名,parameterType指定参数的类型，
        这里直接引用在mybatis.xml中定义的别名-->
    <!-- 在<insert>标签内部编写相应的sql语句，并指定sql参数，
     sql参数使用#{属性名}来获取对象中的属性值 -->
    <insert id="saveUser" parameterType="users" >
        INSERT INTO USER_INFO VALUES(#{uid},#{tel},#{userName})
    </insert>

</mapper>
```

### 第五步：新建MybatisUtil工具类
```
public class MyBatisUtil {

    /*
    * 定义SqlSessionFactory，用于创建SqlSession实例
    * */
    private static SqlSessionFactory sqlSessionFactory;

    /*
    * 在静态代码块中初始化SQLSessionFactory实例
    *
    * */
    static{

        try {
            //通过查找mybatis的核心配置文件，并创建一个输入流用于读取xml文件
            InputStream is = Resources.getResourceAsStream("mybatis.xml");
            //创建一个SqlSessionFactory构建器来实例化一个SqlSessionFactory
            //build方法传入一个输入流来解析配置文件，从而创建SqlSessionFactory
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(is);
        } catch (IOException e) {
            e.printStackTrace();
            throw new RuntimeException("Read mybatis.xml fail.");
        }
    }

    /*
    * 提供一个获取SqlSession的静态方法
    * */
    public static SqlSession getSqlSession(){
        //通过openSession方法来获取sqlSession
        //注意：这个方法可以带一个boolean类型的参数
        //使用不带参数的方法得到的SqlSession是需要我们手动提交事务
        //使用带参数的方法并将其设置为true，这样得到的sqlSession是会自动提交事务
        return sqlSessionFactory.openSession(true);
    }

}
```

### 第六步：指定mapper配置文件的相对路径
在mybatis.xml文件中加入mappers标签
```
    <!--指定mapper配置文件的相对路径-->
    <mappers>
        <mapper resource="mapper/UserMapper.xml"/>
    </mappers>
```

### 第七步：编写Dao接口的实现类
```
public class UserDaoImpl implements UserDao{

    @Override
    public void saveUser(Users user) {
        //获取SqlSession
        SqlSession session = MyBatisUtil.getSqlSession();
        try {
            //执行保存操作
            session.getMapper(UserDao.class).saveUser(user);
        } catch (RuntimeException e) {
            e.printStackTrace();
            session.rollback();
        } finally {
            session.close();
        }

    }
}
```