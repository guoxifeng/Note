# 目录
- [增加 - insert](#增加)
- [删除 - delete](#删除)
- [修改 - update](#修改)
- [查找 - query](#查找)
- [sql标签 - sql](#sql标签)

## 增加

- 增加一条记录 <code>int insert(Domain domain)</code>

```xml
<!--新增一条记录-->
<insert id="insert" keyProperty="id" useGeneratedKeys="true">
    insert into domain_table(field_01, field_02, field_03)
    values (#{field_01}, #{field_02}, #{field_03})
</insert>
```

- 批量新增 <code>int insertBatch(List<Domain> domains)</code>

```xml
<!--批量新增记录-->
<insert id="insertBatch" keyProperty="id" useGeneratedKeys="true">
    insert into table(field_01, field_02, field_03)
    values 
    <foreach collection="list" item="domain" index="index" open="(" separator="),(" close=")">
            #{domain.field_01}, #{domain.field_02}, #{domain.field_03},
    </foreach>
</insert>
```

## 删除

- 根据id删除一条记录 <code>int deleteById(Long id)</code>

```xml
<!--通过主键删除-->
<delete id="deleteById">
    delete from table where id = #{id}
</delete>
```

- 根据ids批量删除记录 <code>int deleteByIds(List<Long> ids)</code>

```xml
<!--通过主键批量删除-->
<delete id="deleteByIds" parameterType="java.util.List">
    delete from table
    where id in
    <foreach collection="list" item="id" index="index" open="(" separator="," close=")">
        #{id}
    </foreach>
</delete>
```

## 修改

- 根据id修改 <code>int update(Domain domain)</code>

```xml
<!--通过主键修改数据-->
<update id="update">
    update table
    <trim prefix="set" suffixOverrides="," suffix="where id = #{id}">
        <include refid="update_field"/>
    </trim>
</update>
```

- 批量修改单独写 需要特别注意不要修改全部数据

## 查找

- 根据id查找一个实体类 <code>Domain queryById(Long id)</code>

```xml
<!--查询单个-->
<select id="queryById" resultMap="DomainMap">
    select <include refid="columns"/>
    from table
    where id = #{id}
</select>
```

- 查询一个字段

```java
@Select("select name from table where id = #{id}")
String queryFieldById(@Param("id") Long id);
```

- 查询一列数据并按照更新时间倒叙输出

```java
@Select("select distinct(name) from table where field = #{field}  order by update_time desc")
List<Object> queryFields(@Param("field") Object field);
```

- 查询所有并按照更新时间倒叙输出 <code>List<Domain> queryAll(Domain domain, @Param("offset") int offset, @Param("limit") int limit)</code>

```xml
<!-- 根据条件查询所有 -->
<select id="findAll" resultMap="DomainMap">
    select <include refid="columns"/>
    from table
    <trim prefix="where" prefixOverrides="and">
        <include refid="find_field"/>
    </trim>
    order by update_time desc
    limit #{offset}, #{limit}
</select>
```

- 查询所有总数 <code>int queryAllTotal(Domain domain)</code>

```xml
<!-- 根据条件查询总数 -->
<select id="queryAllTotal" resultType="java.lang.Integer">
    select count(id)
    from table
    <trim prefix="where" prefixOverrides="and">
        <include refid="find_field"/>
    </trim>
</select>
```

# sql标签

- 列名定义

```xml
<sql id="columns">
    id, field_01, field_02
</sql>
```

- 条件语句定义

```xml
<!-- 查询字段 -->
<sql id="find_field">
    <if test="id != null"> and id = #{id}</if>
    <if test="field != null and field != ''"> and field = #{field} </if>
</sql>

<!-- 更新字段 -->
<sql id="update_field">
    <if test="field_01 != null"> field_01 = #{field_01}, </if>
    <if test="field_02 != null and field_02 != ''"> field_02 = #{field_02}, </if>
</sql>
```

- 使用

```xml
<include refid="columns"/>
```

# foreach 标签

- 入参为<code>parameterType="java.util.List"</code>

```xml
<delete id="deleteByIds" parameterType="java.util.List">
    delete from table
    where id in
    <foreach collection="list" item="id" index="index" open="(" separator="," close=")">
        #{id}
    </foreach>
</delete>
```

- 入参为<code>parameterType="java.util.Map"</code>

```java
// 入参
HashMap map = new HashMap();
map.put("ids", idList);
map.put("status", 1);
```

```xml
<!-- 入参:HashMap map -->
<select id="findAllByBorrowUserAndIds" resultMap="AgentMap" parameterType="java.util.Map">
    select t
    from table t
    where t.id in
    <foreach collection="ids" item="id" index="index" open="(" separator="," close=")">
        #{id}
    </foreach>
    and status = #{status}
</select>
```

# trim 标签

- 各个属性含义

```

```

- 动态更新

```xml
<update id="update">
    update table
    <trim prefix="set" suffixOverrides="," suffix="where id = #{id}">
        <include refid="update_field"/>
    </trim>
</update>
```

- 动态查找

```xml
<select id="query" resultMap="DomainMap">
    select <include refid="columns"/>
    from table
    <trim prefix="where" prefixOverrides="and">
        <include refid="find_field"/>
    </trim>
    order by update_time desc
</select>
```

# resultMap 标签

- 实体类与表字段映射关系: 返回类型必需使用其接收

```xml
<resultMap type="Domain" id="DomainMap">
    <result property="id" column="id" jdbcType="INTEGER"/>
    <result property="createTime" column="create_time" jdbcType="TIMESTAMP"/>
    <result property="name" column="name" jdbcType="VARCHAR"/>
</resultMap>
```