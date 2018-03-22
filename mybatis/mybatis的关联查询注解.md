# 结合注解的关联查询
## 第一步：定义接口
```
 	Users findUserById4(int uid);

    Users findUser(String userName ,String tel);

    Users findUser2(Map<String , Object> map);

    Users findUser3(Users user);
```
关联查询，使用@ResultMap指定映射配置
```
@Select("SELECT * FROM USER_INFO U INNER JOIN CARD_INFO C ON C.U_ID = U.U_ID " +
		"INNER JOIN TEL_INFO T ON T.U_ID = U.U_ID WHERE U.U_ID=#{id}")
@ResultMap("edu.nf.ch09.dao.UserDao.userMap")
Users findUserById4(int uid);
```
使用@Param映射多个查询参数
```
@Select("SELECT * FROM USER_INFO U INNER JOIN CARD_INFO C ON C.U_ID = U.U_ID " +
		"INNER JOIN TEL_INFO T ON T.U_ID = U.U_ID WHERE U.U_NAME=#{uname} AND T.T_NUMBER = #{telNumber}")
@ResultMap("edu.nf.ch09.dao.UserDao.userMap")
Users findUser(@Param("uname") String userName ,@Param("telNumber") String tel);
```
将多个查询参数封装成map对象
```
@Select("SELECT * FROM USER_INFO U INNER JOIN CARD_INFO C ON C.U_ID = U.U_ID " +
		"INNER JOIN TEL_INFO T ON T.U_ID = U.U_ID WHERE U.U_NAME=#{uname} AND T.T_NUMBER = #{telNumber}")
@ResultMap("edu.nf.ch09.dao.UserDao.userMap")
Users findUser2(Map<String , Object> map);
```
将多个查询参数封装成实体对象
```
@Select("SELECT * FROM USER_INFO U INNER JOIN CARD_INFO C ON C.U_ID = U.U_ID " +
		"INNER JOIN TEL_INFO T ON T.U_ID = U.U_ID WHERE U.U_NAME=#{uname} AND C.C_NUMBER=#{card.cardNumber}")
@ResultMap("edu.nf.ch09.dao.UserDao.userMap")
Users findUser3(Users user);
```

## 第二步：配置mapper文件
在resource/mapper目录下新建UserMapper.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="edu.nf.ch09.dao.UserDao">

    <!-- 配置UserMap -->
    <resultMap id="userMap" type="users">
        <id property="uid" column="U_ID"/>
        <result property="userName" column="U_NAME"/>
        <association property="card" resultMap="cardMap"/>
        <collection property="tels" resultMap="telMap"/>
    </resultMap>

    <resultMap id="cardMap" type="idCard">
        <id property="cid" column="C_ID"/>
        <result property="cardNumber" column="C_NUMBER"/>
    </resultMap>

    <resultMap id="telMap" type="tel">
        <id property="tid" column="T_ID"/>
        <result property="telNumber" column="T_NUMBER"/>
    </resultMap>
</mapper>
```

## 第三步：配置Mybatis核心配置文件
```
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>

    <typeAliases>
        <package name="edu.nf.ch09.entity"/>
    </typeAliases>

    <environments default="mysql">
        <environment id="mysql">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <!--<mapper class="edu.nf.ch09.dao.UserDao"/>-->
        <mapper resource="mapper/UserMapper.xml"/>
    </mappers>
</configuration>
```