# 基于注解的实体映射配置
## 第一步：定义dao接口
```
public interface UserDao {

    void saveUser(Users user);

    void updateUser(Users user);

    void deleteUser(String uid);
	
	.....
}
```
### @Insert
定义在接口方法上，等同于mapper配置文件中的Insert标签
```
@Insert("INSERT INTO USER_INFO VALUES(#{uid},#{tel},#{userName})")
@SelectKey(
		keyProperty = "uid" ,
		before = true,
		resultType = String.class ,
		statement = "SELECT UUID() FROM DUAL")
void saveUser(Users user);
```
主键生成策略：
1.使用@Options来实现自增长，将useGeneratedKeys设置为true,表示启用自增长
	keyProperty指定实体中的id主键
@Options(useGeneratedKeys = true , keyProperty = "uid")

2.使用@SelectKey注解来实现Sequence或UUID的主键生成策略,
keyProperty指定实体的id主键，resultType指定生成主键的类型
statement指定执行数据库的主键生成函数语句，before设置为true
表示在执行SQL之前生成主键
@SelectKey(keyProperty = "uid" ,before = true,resultType = String.class , statement = "SELECT UUID() FROM DUAL")

### @Update
```
@Update("UPDATE USER_INFO SET USER_NAME=#{userName} WHERE U_ID=#{uid}")
void updateUser(Users user);
```
### @Delete
```
@Delete("DELETE FROM USER_INFO WHERE U_ID=#{id}")
void deleteUser(String uid);
```

### @Select
以映射别名的方式封装到结果集
```
@Select({"SELECT U_ID AS uid,U_NAME AS userName FROM USER_INFO WHERE U_ID = #{id}"})
Users findUserById(int uid);
```

 使用@Results注解来映射结果集
 在这个注解中使用多个@Result来映射每一个字段和表的列
 ```
@Select("SELECT * FROM USER_INFO WHERE U_ID=#{id}")
@Results({
		@Result(property = "uid",column = "U_ID"),
		@Result(property = "userName" , column = "U_NAME")
})
Users findUserById2(int uid);
 ```
 
使用@ResultMap来映射结果集，结合xml来配置resultMap
```
@Select("SELECT * FROM USER_INFO WHERE U_ID=#{id}")
//@ResultMap的value对应的是xml中的namespace的值加上resultMap的id
@ResultMap("edu.nf.ch09.dao.UserDao.userMap")
Users findUserById3(int uid);
```

## 第二步：注册mapper
配置mybatis核心配置文件
```
<mappers>
	
	<!--  当dao接口中使用了@ResultMap,优先配置xml文件  -->
	<!--<mapper resource="mapper/UserMapper.xml"/>-->
	
	
	<!-- 使用注解时，这里使用class属性指定Dao接口的完整类名 -->
	<mapper class="edu.nf.ch08.dao.UserDao"/>

</mappers>
```