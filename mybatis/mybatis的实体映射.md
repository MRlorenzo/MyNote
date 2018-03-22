# mybatis的实体映射

## resultType属性：
使用resultType映射查询结果的类型，这里为实体的类型，引用实体的别名即可
注意：当使用resultType来映射实体对象时，必须列名和字段名相同才可以映射
        如果字段名和列名不相同，那么就需要使用as关键字来映射实体的字段名
```
	<select id="findUserById" parameterType="java.lang.String" resultType="users">
		SELECT U_ID AS uid,TEL AS tel,USER_NAME AS userName FROM USER_INFO WHERE U_ID = #{id}
	</select>
	
    <select id="findUsers" resultType="users">
        SELECT U_ID AS uid,TEL AS tel,USER_NAME AS userName FROM USER_INFO
    </select>

    <select id="countUser" resultType="int">
        SELECT COUNT(*) FROM USER_INFO
    </select>

    <!-- 将单条查询结果映射成Map对象 -->
    <select id="findUserByIdToMap" parameterType="java.lang.String" resultType="java.util.Map">
        SELECT * FROM USER_INFO WHERE U_ID = #{id}
    </select>

    <select id="findUsersToMap" resultType="java.util.Map">
        SELECT * FROM USER_INFO
    </select>
```

## resultMap属性：
定义resultMap,ID作为唯一标示，type指定类型，这里引用实体的别名 
```
    <resultMap id="userMap" type="users">
        <!-- 映射主键，property指定实体的字段名，column指定表的列名 -->
        <id property="uid" column="U_ID"/>
        <!-- 映射其他字段 -->
        <result property="tel" column="TEL"/>
        <result property="userName" column="USER_NAME"/>
    </resultMap>
```
使用resultMap映射结果集， resultMap属性引用上面声明的resultMap的id
```
    <select id="findUserById" parameterType="java.lang.String" resultMap="userMap">
        SELECT * FROM USER_INFO WHERE U_ID=#{id}
    </select>
    
    <select id="findUsers" resultMap="userMap">
        SELECT * FROM USER_INFO
    </select>
```

## 模糊查询
```
    <select id="findUsers" parameterType="java.lang.String" resultMap="userMap">
        SELECT * FROM USER_INFO WHERE TEL LIKE #{tel}"%"
    </select>
```

## 条件查询
### 动态拼接条件查询
使用where标签配置，在if中判断map的key是否为空，
            不为空则拼接到sql语句中
```
<select id="findByCondition1" parameterType="java.util.Map" resultMap="userMap">
	SELECT * FROM USER_INFO
	<where>
		<if test="uname != null and uname != ''">
			AND USER_NAME = #{uname}
		</if>
		<if test="tel != null and tel != ''">
			AND TEL = #{tel}
		</if>
	</where>
</select>
```
### 动态选择条件
使用choose标签配置 ,otherwise是在选择条件之后的一个可选项
```
<select id="findByCondition2" parameterType="java.util.Map" resultMap="userMap">
	SELECT * FROM USER_INFO
	<choose>
		<when test="uname != null and uname != '' ">
			WHERE USER_NAME = #{uname}
		</when>
		<when test="tel != null and tel != '' ">
			WHERE TEL = #{tel}
		</when>
		<otherwise>
			ORDER BY USER_NAME DESC
		</otherwise>
	</choose>
</select>
```
### 循环迭代参数
```
<select id="findByCondition3" parameterType="java.util.Map" resultMap="userMap">
	SELECT * FROM USER_INFO
	<where>
		<if test="telList != null">
			<foreach collection="telList" item="t">
				OR TEL = #{t}
			</foreach>
		</if>
	</where>
</select>
```
### in子查询
例如：SELECT * FROM USER_INFO WHERE TEL IN ('','','')
```
<select id="findByCondition4"  parameterType="java.util.Map" resultMap="userMap">
	SELECT * FROM USER_INFO
	<where>
		TEL IN
		<if test="telList != null">
			<foreach collection="telList" item="t" open="(" separator="," close=")">
				#{t}
			</foreach>
		</if>
	</where>
</select>
```
### 动态更新字段
```
<update id="updateUser" parameterType="users">
	UPDATE USER_INFO
	<set>
		<if test="userName != null and userName !='' ">
			USER_NAME = #{userName},
		</if>
		<if test="tel != null and tel != '' ">
			TEL = #{tel}
		</if>
	</set>
	WHERE U_ID = #{uid}
</update>
```