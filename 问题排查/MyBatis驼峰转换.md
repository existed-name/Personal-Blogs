# MyBatis驼峰转换


---

## 1、问题描述
用MyBatis查询数据时，出现了`null`，之后发现原因是Java对象的属性名 ≠ 数据库字段名，比如属性是`updateTime`，但在数据库里面是`update_time`，于是`SELECT updateTime FROM user_table`就找不到，只能返回`null`



## 2、解决方法
### （1）开启`自动驼峰转换`
①在`application.yaml`(yml)中添加如下配置  
```yaml
mybatis:
  configuration:
    map-underscore-to-camel-case: true
```
它可以让数据库字段的**下划线命名**转换为Java对象属性的**小驼峰命名**，也就是`abc_def` → `abcDef`

②注意`mybatis`和`spring`同级、都缩进0个空格

③不过没有反向的`map-camel-case-to-underscore`


### （2）SQL语句取别名
```sql
# as 可以省略
SELECT update_time AS updateTime FROM user_table
# 数据库字段名 AS 对象属性名
```
这样就查的就是下划线命名的字段了


### （3）手动结果映射
通常在**命名不规则**的时候才手动映射，比如属性名是`id`，字段名是`user_id`，不能自动转换，只能手动映射

#### **①`@Results`**
```java
    @Results( id = "userMap", value = { // Results装了一个Result数组
            // 标记 id 然后在其他方法上面引用，就可以不用重复写命名转换
            @Result( property = "createTime", column = "create_time" ),
            // 如果某个 Result 是主键，就补充 id = true
            @Result( property = "updateTime", column = "update_time" )
    } )
    @Select( "SELECT * FROM user_table" )
    public List< User > findAll();
    
    @ResultMap( "userMap" )
    @Select( "SELECT * FROM user_table WHERE id = #{id}" )
    public User findById( @Param( "id" ) Long id );
```
用在`Mapper`接口的方法上面，把指定字段名转换为属性名

#### **②`ResultMap`**
用于`Mapper.xml`文件
```xml
    <resultMap id="userMap" type="com.公司名.项目名.pojo.User">
        <!-- resultMap 的 id 只在 xml 文件内使用，不需要在 Mapper 接口定义 userMap -->
        <id property="id" column="id" /> 
        <!-- 只需要指定数据库表的主键、需要手动映射的名字 -->
        <result property="createTime" column="create_time" />
        <result property="updateTime" column="update_time" />
    </resultMap>
    
    <select id="findAll" resultMap="userMap">
        <!-- 按照 userMap 的转换规则替换名字 -->
        SELECT * FROM user_table
    </select>
```
> 自闭合标签`/>`等价于 `<>  中间没有东西  </>`  
>   
> 因为目标在前，源头在后所以通常property前column后😂不过我想的是`column to property`，所以column前、property后应该也可以，不过`@Result`、`resultMap`没有限制顺序，只是指定值  
>   
> 因为`resultMap`比`@Results`长串注解更清晰&功能更多，所以比较复杂的SQL用XML的`resultMap`

---

