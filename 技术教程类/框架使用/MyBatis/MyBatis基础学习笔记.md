# `MyBatis`基础学习笔记

---

## 1、配置
（1）在创建SpringBoot项目时选中`MySQL Driver`(实现Java连接&操作数据库)、`MyBatis Framework`，或者手动在`pom.xml`添加依赖  

（2）在`src/main/resources`包的`application.properties`文件中添加数据库和`MyBatis`的配置
```xml
# 数据库的url地址
spring.datasource.url=jdbc:mysql://localhost:3306/web
# 数据库驱动类类名
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
# 用户名
spring.datasource.username=用户名
# 密码
spring.datasource.password=密码

# mybatis的配置
mybatis.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl
```

（3）在`src/main/java`的`com.公司名.项目名`下创建`mapper`包，存放各种`Mapper`接口，用来访问数据库拿取数据。比如`UserMapper`访问数据库中的`user_table`表



## 2、常用操作
### （1）总结
①简单的SQL可以写在`Mapper`接口里面，稍微复杂就写在`Mapper.xml`文件里面

②给接口中的方法标上`@Insert/Delete/Update/Select`注解表示增删改查，然后在注解的小括号（注解的value）里面写SQL

③`MyBatis`会创建一个`Mapper`接口的实现类对象，放进Ioc控制反转容器，再注入到使用了`Mapper`对象的地方


### （2）代码示例
```java
@Mapper
public interface UserMapper {
    /**
     * 查询所有用户
     */
    @Select( "SELECT * FROM user" )
    public List< User > findAll();

    /**
     * 删除特定年龄的用户
     *
     * 往这个方法传入的参数 age 会替代 SQL 注解里的 #{age}
     */
    @Delete( "DELETE FROM user WHERE age = #{age}" )
    public void deleteByAge( Integer age );

    /**
     * 修改特定 id 用户的密码
     */
    @Update( "UPDATE user SET password = #{password} WHERE id = #{id}" )
    public void updatePasswordById( String password, Long id );

    /**
     * 添加用户
     * 由于id是auto_increment，所以不需要写id
     *
     * 这里的参数 User 是为了让 MyBatis 调用 user 的 getter 把数值传进 SQL，
     * 就不需要一个一个传入参数
     */
    @Insert( "INSERT INTO user ( username, password, name, age ) " +
            "values( #{username}, #{password}, #{name}, #{age} )" )
    public void insertUser( User user );
}
```


### （3）`#{xxx}`占位符
#### **①`#{对象属性名}`**
对象的属性是`getter/setter`暴露出来的东西（可以简单看作成员变量，但≠成员变量）

比如`getEntryDate`，属性就是获取到的`entryDate`，写的时候就是`#{entryDate}`（`getXxxYyy`，那么属性名就是`xxxYyy`）

#### **②`#{xxx}` 占位符**
```sql
sql = "SELECT * FROM users WHERE username = #{username} AND password = #{password}";
```
MyBatis把SQL转换为JDBC的预编译SQL，`#{}`占位符用`?`替代，然后调用对应的`getter`获取值传入SQL

#### **③JDBC预编译SQL**
```java
String sql = "SELECT * FROM users WHERE username = ? AND password = ?";
PreparedStatement ps = connection.prepareStatement( sql );
ps.setString( 1, "管理员" );
ps.setString( 2, "123456" );

=> sql = "SELECT * FROM users WHERE username = '管理员' AND password = '123456'";
```
表面上是给`String`包裹单引号，但它是直接把`?`作为普通字符串处理，不会再去解析`?`里面的SQL关键字，因为已经是普通字符串了（当然，跟单引号包裹的效果差不多，但是理解起来需要转弯😂）

#### **④`${xxx}` 拼接符**
```sql
sql = "SELECT * FROM users WHERE username = '${username}' AND password = '${password}'";
<=> 原始的"SELECT * FROM users WHERE username = '" + 用户输入 + "' AND password = '" + 用户输入 + "'";
```
也就是字符串拼接，不过注意`'${xxx}'`还需要自己写单引号，它是直接把字符串的值拼进去（`String = "值"`，双引号只是表示他是字符串字面量、而不是一个变量的名字）


### （4）`@Param`注解
1. `Mapper`里的方法，出现**多个参数**（不管是相同类型还是不同类型），都建议给参数**标记`@Param`**，来告诉MyBatis这个参数对应了SQL中的哪个值

​2. 参数如果标记为`@Param( "xyz" )`，就会指定MyBatis去SQL里面找`#{xyz}`

​3. 这样就明确表示方法参数对应了SQL里面要绑定值的`#{xxx}`，而不是简单的几个数据类型

4. 比如  
```java
    /**
     * 根据用户名和密码查询用户信息
     *
     * 2 个 String 参数都明确指定名字（对应了 SQL 中的哪个数据）
     */
    @Select( "SELECT * FROM user WHERE username = #{username} AND password = #{password}" )
    public User findByUsernameAndPassword( @Param( "username" ) String username, @Param( "password" ) String password );
```



## 3、`Mapper.xml`
（1）简单的SQL写在`Mapper`接口，复杂一点就写在xml文件里面

（2）位置：放在`src/main/resources`下，和`Mapper`接口的包名一样(`com.公司名.项目名.mapper`)

（3）编写`DTD约束`，直接AI生成/[MyBatis官网](https://mybatis.org/mybatis-3/zh_CN/getting-started.html)(进去界面的“探究已映射的 SQL 语句”那一节)复制
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="...">
	<!-- 命名空间namespace = com.公司名.项目名.mapper.具体的Mapper接口名 -->
</mapper>
```

（4）然后在`<mapper></mapper>`标签里面写SQL
```xml
    <select id="" resultType="">
        <!--
            id: Mapper接口中的方法名
            resultType: 查询到的【单条数据】的类型
            比如 id = findAll 查找所有用户，返回 User 而不是 List< User >
        -->
        SELECT * FROM user
    </select>
```



## 4、`MyBatisX`插件
![下载插件](/assets/images/技术教程类/框架使用/MyBatis/MyBatis基础学习笔记/01-MyBatisX.png "下载插件")  
👇点击小鸟🐦图标可以跳到对应的`Mapper`接口（：其他的功能还在研究~~~
![xml文件和接口类的小鸟颜色不一样](/assets/images/技术教程类/框架使用/MyBatis/MyBatis基础学习笔记/02-ClickTheBird.png "xml文件和接口类的小鸟颜色不一样")  



## 5、相关文章
> <u>[`MyBatis`配置SQL注解提示](/技术教程类/MyBatis配置SQL注解提示.md)</u>  
> <u>[`MyBatis`配置`Mapper.xml`模板](/技术教程类/MyBatis配置Mapper.xml模板.md)</u>  
> <u>[解决`.properties`文件中文乱码问题](/问题排查/开发工具与IDE/IDEA/文件编码/解决.properties文件中文乱码问题.md)</u>  

---


