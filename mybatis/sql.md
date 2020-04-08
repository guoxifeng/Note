## 增加

- 增加一条记录

<code>insert(Domain domain)</code>

```xml
<!--新增一条记录-->
<insert id="insert" keyProperty="id" useGeneratedKeys="true">
    insert into domain_table(field_01, field_02, field_03)
    values (#{field_01}, #{field_02}, #{field_03})
</insert>
```

- 批量新增

<code>insert(List<Domain> domains)</code>

```xml
<!--批量新增记录-->
<insert id="insert" keyProperty="id" useGeneratedKeys="true">
    insert into domain_table(field_01, field_02, field_03)
    values 
    <foreach collection="list" item="domain" index="index" open="(" separator="),(" close=")">
            #{domain.field_01}, #{domain.field_02}, #{domain.field_03},
    </foreach>
</insert>
```

## 删除

## 修改

## 查找