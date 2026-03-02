# `PageHelper`简介


---

## 1、描述
### (1)
因为一次性查询所有内容，数据量太大，很费性能，所以我们需要分页查询


### (2)
对于员工服务类`EmpServiceImpl`，手动分页查询需要记录**总共的数据条数**、当前要查询的**部分员工数据**，包装成`PageResult`或者`PageBean`返回给`EmpController`

```java
    /**
     * 分页查询
     * @param page 当前页码(想要看的那1页)
     * @param pageSize 每页记录数(每页有多少行数据/多少个员工)
     */
    @Override
    public PageResult page( Integer page, Integer pageSize ){
        //1. 总记录数
        Long total = empMapper.count();

        //2. 查询员工
        Integer pageStart = ( page - 1 ) * pageSize;
        List< Emp > empList = empMapper.list( pageStart, pageSize );

        //3. 封装结果
        return new PageResult( total, empList );
    }
```

MySQL分页查询`LIMIT  起始索引  查询数据条数`，代码中的`pageStart`、`pageSize`分别表示`起始索引`、`查询数据条数`，也就是从索引`pageStart`开始找出`pageSize`行数据；而传进来的参数`page`表示用户想看的那一页，于是我们需要手动计算出**那一页对应的起始索引**才可以拿去查询

* 第1页第1条数据的索引是0，每页有`pageSize`条数据

* 于是第2页的第1条数据索引 = 0 + pageSize = pageSize

* 第3页第1条数据索引 = 第2页第1条索引`pageSize` + `pageSize` = 2 * pageSize

* 所以**第page页的第1条数据的索引 = ( page - 1 ) * pageSize**


### (3)
这里是对应的`EmpMapper`接口

```java
    /**
     * 查询总记录数
     */
    @Select( "SELECT count( * ) FROM emp " )
    Long count();

    /**
     * 分页查询所有员工
     */
    @Select( "SELECT * FROM emp LIMIT #{pageStart}, #{pageSize}" )
    List< Emp> list( @Param( "pageStart" ) Integer pageStart, @Param( "pageSize" ) Integer pageSize );
```


### (4)问题
需要手动维护记录数、分页查询索引，比较麻烦



## 2、引入
### (1)
于是可以把分页的工作交给PageHelper插件完成，我们只管查员工列表


### (2)添加依赖
在`pom.xml`文件添加PageHelper的依赖(<u>[中央仓库](https://mvnrepository.com/artifact/com.github.pagehelper/pagehelper-spring-boot-starter)</u>、<u>[官网](https://pagehelper.github.io/)</u>)

```xml
<!-- Source: https://mvnrepository.com/artifact/com.github.pagehelper/pagehelper-spring-boot-starter -->
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.4.7</version>  <!-- 看自己需不需要更新的版本 -->
    <scope>compile</scope>
</dependency>
```


### (3)配置`application.yaml`
```yaml
# PageHelper配置
pagehelper:
  # 启用分页参数合理化
  # 如果设置为 true，当请求的分页页码 < 1 时，它会自动查询第一页
  # 当请求页码 > 总页数时，它会自动查询最后一页
  # 这样可以防止因参数错误导致返回空列表
  reasonable: true
  # 指定数据库方言
  helper-dialect: mysql
```



## 3、优化代码
### (1)`EmpMapper`

```java
    /**
     * 分页查询所有员工
     */
    @Select( "SELECT * FROM emp" )
    List< Emp> list(  );
```


### (2)`EmpServiceImpl`

```java
    /**
     * 分页查询
     * @param page 页码
     * @param pageSize 每页记录数
     */
    @Override
    public PageResult page( Integer page, Integer pageSize ){
        //1. 设置分页参数
        PageHelper.startPage( page, pageSize );

        //2. 查询
        List< Emp > empList = empMapper.list();
        Page< Emp > empPage = ( Page< Emp > ) empList;

        //3. 封装结果
        return new PageResult( empPage.getTotal(), empPage.getResult() );
    }
```

首先把要查询的那一页、查询条数塞给PageHelper，它会自动查到总共的数据条数、记录下来，然后我们查询员工数据时，它会把分页查询的`LIMIT  起始索引  查询数据条数`拼接到`Mapper`的SQL语句后面——**于是注意不能添加分号，否则它会拼接到分号后面**，我们查到的`empList`转换类型成PageHelper的`Page`类——`Page`类继承自`ArrayList`，利用多态，可以把`List`具体化为`Page`，然后从这个`Page`里面拿到封装好的数据，返回给`Controller`



## 4、附
### (1)
* 需要注意，为了安全，PageHelper**只会操作1次分页查询**，每一次`startPage`设置参数，紧跟需要分页的查询，后续如果还需要分页、就再调用1次PageHelper

* 后续改进为更复杂的条件分页查询、传入包装多个查询参数的对象，依然可以按照预期分页展示

* `Page`可以优化成更全能的`PageInfo`

```java
PageInfo<Emp> pageInfo = new PageInfo<>(empList);
```


### (2)效果展示
ApiFox请求数据: `http://localhost:8080/emps?page=1&pageSize=5`
![](/assets/images/技术教程类/PageHelper简介/01-Test.png "")  

如果没开PageHelper的`参数合理化`配置，查询错误的`page = -1`会返回空列表👇
![](/assets/images/技术教程类/PageHelper简介/02-WrongParameter.png "")  

---
> **往期文章**  
> <u>[ApiFox基本使用](/技术教程类/开发工具&插件/ApiFox/ApiFox基本使用.md)</u>  
> <u>[IDEAMaven项目打包](/技术教程类/项目打包/IDEAMaven项目打包.md)</u>  
> <u>[MyBatis驼峰转换](/技术教程类/框架使用/MyBatis/MyBatis驼峰转换.md)</u>  
> <u>[MyBatis基础学习笔记](/技术教程类/框架使用/MyBatis/MyBatis基础学习笔记.md)</u>  
> <u>[MyBatis配置Mapper.xml模板](/技术教程类/框架使用/MyBatis/MyBatis配置Mapper.xml模板.md)</u>  
> <u>[MyBatis配置SQL注解提示](/技术教程类/框架使用/MyBatis/MyBatis配置SQL注解提示.md)</u>  
> <u>[一道SQL分组过滤题](/数据结构与算法刷题/SQL/一道SQL分组过滤题.md)</u>  
> <u>[试一下手写having关键字](/技术探索&评测/试一下手写having关键字.md)</u>  
> ……  

