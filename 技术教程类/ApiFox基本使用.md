# ApiFox基本使用


---

## 1、简介
浏览器地址栏的链接只能发送GET(查询)请求，这里用ApiFox测试URL + 增删改查操作
> ApiFox 是一个 API 管理和调试工具，帮助开发者方便地管理、测试、调试和监控 API 请求和响应。它通常用于简化 API 调试过程，支持接口文档生成、请求模拟、数据分析等功能，有助于提升开发效率和测试精度



## 2、安装
（1）在<u>[ApiFox官网](https://apifox.com/)</u>下载应用 → 打开安装器
![默认选项即可](/assets/images/技术教程类/ApiFox基本使用/01-OfficialWebsite.png "默认选项即可")  
👇网页端需要下插件  
![在线使用](/assets/images/技术教程类/ApiFox基本使用/02-UseOnline.png "在线使用")  

（2）安装选项：默认的`仅为我安装`  
![仅为我安装](/assets/images/技术教程类/ApiFox基本使用/03-InstallForMeOnly.png "仅为我安装")  

（3）选择安装位置  
![选择安装位置](/assets/images/技术教程类/ApiFox基本使用/04-SelectInstallDirctory.png "选择安装位置")  

（4）等待安装成功  
![安装完成](/assets/images/技术教程类/ApiFox基本使用/05-FinishInstall.png "安装完成")  



## 3、基本使用
### (1)新建测试接口
打开应用 → 登录 → `创建空白项目`  
![微信扫码登录](/assets/images/技术教程类/ApiFox基本使用/06-LogIn.png "微信扫码登录")  
![创建空白项目](/assets/images/技术教程类/ApiFox基本使用/07-CreateProject.png "创建空白项目")  
![命名](/assets/images/技术教程类/ApiFox基本使用/08-NameProject.png "命名")  

`新建Http接口`
![新建接口](/assets/images/技术教程类/ApiFox基本使用/09-CreateHttpInterface.png "新建接口")  


### (2)测试
启动SpringBoot项目，把链接（`http://localhost:8080` + `Controller`一个方法的`Mapping`路径）复制到ApiFox里面，然后选择操作类型（增Post删Delete改Put查Get）比如GET，点击`发送`，就可以得到JSON字符串
![接收JSON](/assets/images/技术教程类/ApiFox基本使用/10-TestInterface.png "接收JSON")  

我这里只有`@RequestMapping`，于是不管哪种操作类型，都会走这条路径调用这个方法，就会得到同一种结果
```java
    /**
     * 查询部门列表
     *
     * <u>http://localhost:8080/depts</u>
     */
    @RequestMapping( "/depts" )
    public Result list(){
        List< Dept > deptList = deptService.findAll();
        return Result.success( deptList );
    }
```


### (3)`RequestMapping`衍生注解
要给不同的操作类型指定方法，有2种方式  
①给`@RequestMapping`补充`method`属性
```java
    @RequestMapping( value = "depts", method = RequestMethod.GET )
    // 指定该方法对应GET操作，同理还有RequestMethod.POST/PUT/DELETE...
```

②用`@RequestMapping`的
```java
    @GetMapping( "/depts" )
```
同理还有`@Post/Delete/PutMapping`

指定该查询方法对应`GET`后，再使用同一链接、选择其他操作，就会显示`Method Not Allowed`
![操作类型和注解不一致](/assets/images/技术教程类/ApiFox基本使用/11-MethodNotAllowed.png "操作类型和注解不一致")  


### (4)补充
基础操作只需要把URL粘贴到ApiFox上面、选择操作类型即可，对于其他操作比如
* `@RequestParam`注解: 作为查询参数，筛选条件
```java
    /**
     * 根据id删除部门
     */
    @DeleteMapping( "/depts" )
    public Result delete( @RequestParam( "id" ) Integer deptId ){
        Dept dept = deptService.findById( deptId );
        deptService.deleteById( deptId );
        return Result.success( dept );
    }
```
只需要在ApiFox里面选择`DELETE`、粘贴URL`http://localhost:8080/depts?id=1`就可以删除`id=1`的部门——也可以手动添加参数
![](/assets/images/技术教程类/ApiFox基本使用/12-RequestParam.png "")  

* `@RequestBody`注解: 传入JSON字符串，转换成方法参数里面的对象
```java
    /**
     * 新增部门
     * 请求参数：{"name":"部门名称"}
     */
    @PostMapping( "/depts" )
    public Result add( @RequestBody Dept dept ){
        deptService.add( dept );
        return Result.success( dept );
    }
```
JSON字符串不是放进URL里面的，而是写在ApiFox的`Http接口`的`Body`里面。比如对象的1个属性是`name`，那么JSON可以写作
```json
{
   属性的名字(用双引号包裹) : 值	// 键值对之间用逗号分隔
    				"name" : "研发部"
}
```
![](/assets/images/技术教程类/ApiFox基本使用/13-RequestBody.png "")  

---

