# 使用Provider实现动态sql

## 第一步：定义Provider类
```
 * Provider类中的方法都应该返回一个String类型，
 * 因为返回的都是一个SQL语句的字符串。
 * 方法中使用SQL语句构建器来动态构建sql语句
```
```
public class UserDaoProvider {

    public String saveUser(){
        return new SQL(){
            {
                INSERT_INTO("USER_INFO");
                VALUES("U_ID", "#{uid}");
                VALUES("TEL","#{tel}");
                VALUES("USER_NAME","#{userName}");
            }
        }.toString();
    }

    public String updateUser(Users user){
        return new SQL(){
            {
                UPDATE("USER_INFO");
                if(user.getUserName()!=null){
                    SET("USER_NAME = #{userName}");
                }
                if(user.getTel() != null){
                    SET("TEL = #{tel}");
                }
                WHERE("U_ID = #{uid}");
            }
        }.toString();
    }

    public String deleteUser(){
        return new SQL(){
            {
                DELETE_FROM("USER_INFO");
                WHERE("U_ID=#{id}");
            }
        }.toString();
    }


    public String findUserById(){
        return new SQL(){
            {
                //可以使用一个SELECT方法查询多个列
                //SELECT("U_ID AS uid,TEL AS tel,USER_NAME AS userName");
                //也可以使用多个SELECT方法分别查询不同的列
                SELECT("U_ID AS uid");
                SELECT("TEL AS tel");
                SELECT("USER_NAME AS userName");
                FROM("USER_INFO");
                WHERE("U_ID = #{id}");
            }
        }.toString();
    }

    public String findUserByCondition(){
        return new SQL(){
            {
                SELECT("*");
                FROM("USER_INFO");
                WHERE("USER_NAME = #{uName}");
                WHERE("TEL = #{tel}");
            }
        }.toString();
    }

    /**
     * 当Provider的方法需要参数时，参数类型必须和DAO接口一致。
     * @param map
     * @return
     */
    public String findUserByCondition2(Map<String , Object> map){
        return new SQL(){
            {
                SELECT("*");
                FROM("USER_INFO");
                if(map.get("uName")!=null)
                    WHERE("USER_NAME = #{uName}");
                if(map.get("tel") != null)
                    WHERE("TEL = #{tel}");

            }
        }.toString();
    }
}

```

## 第二步：使用@InsertProvider注解
type指定自定义的provider的class,method指定要调用provider中的方法名
```
public interface UserDao {

    @InsertProvider(type = UserDaoProvider.class , method = "saveUser")
    void saveUser(Users user);

    @UpdateProvider(type = UserDaoProvider.class , method = "updateUser")
    void updateUser(Users user);

    @DeleteProvider(type = UserDaoProvider.class , method = "deleteUser")
    void deleteUser(String uid);

    @SelectProvider(type = UserDaoProvider.class , method = "findUserById")
    Users findUserById(String id);

    /**
     * 多条件查询
     * @param map
     * @return
     */
    @SelectProvider(type = UserDaoProvider.class , method = "findUserByCondition")
    @ResultMap("edu.nf.ch10.dao.UserDao.userMap")
    Users findUserByCondition(Map<String , Object> map);

    /**
     * 动态条件查询
     * @param map
     * @return
     */
    @SelectProvider(type = UserDaoProvider.class , method = "findUserByCondition2")
    @ResultMap("edu.nf.ch10.dao.UserDao.userMap")
    Users findUserByCondition2(Map<String , Object> map);

}

```
