# mybatis关联查询的一些例子

## 数据库：
```
CREATE TABLE `USER_INFO` (
  `U_ID` int(11) NOT NULL AUTO_INCREMENT,
  `U_NAME` varchar(100) DEFAULT NULL,
  PRIMARY KEY (`U_ID`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```
```
CREATE TABLE `CARD_INFO` (
  `C_ID` int(11) NOT NULL AUTO_INCREMENT,
  `C_NUMBER` varchar(100) DEFAULT NULL,
  `U_ID` int(11) DEFAULT NULL,
  PRIMARY KEY (`C_ID`),
  UNIQUE KEY `U_ID` (`U_ID`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```
```
CREATE TABLE `ADDR_INFO` (
  `A_ID` int(11) NOT NULL AUTO_INCREMENT,
  `ADDRESS` varchar(200) DEFAULT NULL,
  PRIMARY KEY (`A_ID`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```
```
CREATE TABLE `TEL_INFO` (
  `T_ID` int(11) NOT NULL AUTO_INCREMENT,
  `T_NUMBER` varchar(100) DEFAULT NULL,
  `U_ID` int(11) DEFAULT NULL,
  PRIMARY KEY (`T_ID`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```
```
CREATE TABLE `USER_ADDR` (
  `U_ID` int(11) DEFAULT NULL,
  `A_ID` int(11) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```

## 实体类：
```
public class Users {
    private int uid;
    private String userName;

    //一对一关联身份证
    private IdCard card;

    //一对多关联联系电话
    private List<Tel> tels = new ArrayList<>();

    //多对多关联住址
    private List<Address> addresses = new ArrayList<>();
	
	//getter,setter略
}
```
```
public class IdCard {

    private int cid;
    private String cardNumber;
	
	//getter,setter略
}
```
```
public class Tel {

    private int tid;
    private String telNumber;
	
	//getter,setter略
}
```
```
public class Address {

    private int aid;
    private String address;
	
	//getter,setter略
}
```

## mapper配置文件：
关联实体必须先配置要关联的实体的resultMap
* 配置card的resultMap
```
<resultMap id="cardMap" type="idCard">
	<id property="cid" column="C_ID"/>
	<result property="cardNumber" column="C_NUMBER"/>
</resultMap>
```
* 配置tel的resultMap
```
<resultMap id="telMap" type="tel">
	<id property="tid" column="T_ID"/>
	<result property="telNumber" column="T_NUMBER"/>
</resultMap>
```
* 配置address的resultMap
```
<resultMap id="addrMap" type="address">
	<id property="aid" column="A_ID"/>
	<result property="address" column="ADDRESS"/>
</resultMap>
```
* 配置user的resultMap
```
    <!-- 配置User的resultMap -->
    <resultMap id="userMap" type="users">
        <id property="uid" column="U_ID"/>
        <result property="userName" column="U_NAME"/>
		
        <!-- 一对一关联身份证,property指定关联的字段，resultMap引用card的resultMap的id -->    
        <association property="card" resultMap="cardMap"/>

        <!-- 一对多关联联系电话 -->
        <collection property="tels" resultMap="telMap"/>

        <!-- 使用collection多对多关联地址 -->            
        <collection property="addresses" resultMap="addrMap"/>
    </resultMap>
```
"一对一" 或者 "多对一" 使用 association标签映射
"一对多" 或者 "多对多" 使用 collection标签映射

## 关联查询
```
<select id="findUserById" parameterType="int" resultMap="userMap">
	SELECT * FROM USER_INFO U
	INNER JOIN CARD_INFO C ON U.U_ID = C.U_ID
	LEFT JOIN TEL_INFO T ON U.U_ID = T.U_ID
	INNER JOIN USER_ADDR UA ON UA.U_ID = U.U_ID
	INNER JOIN ADDR_INFO A ON A.A_ID = UA.A_ID
	WHERE U.U_ID = #{id}
</select>
```