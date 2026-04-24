# `MyBatis`配置SQL注解提示

> <u>[个人博客仓库](https://github.com/existed-name/Personal-Blogs)</u>
---

## 1、描述
在`Mapper`接口的方法注解(`@Insert/Delete/Update/Select`)里面写SQL没有语法提示，需要手动设置


## 2、配置
### （1）情况1：没有设置语言注入
右键注解内双引号包裹的SQL语句 → `Show Context Actions` → `Inject language or reference`  → 找到并选中`MySQL`
![点击Show Context Actions](/assets/images/技术教程类/框架使用/MyBatis/MyBatis配置SQL注解提示/01-ShowContextActions.png "点击Show Context Actions")  
![点击Inject language or reference](/assets/images/技术教程类/框架使用/MyBatis/MyBatis配置SQL注解提示/02-InjectlanguageOrReference.png "点击Inject language or reference")  
![往下翻，找MySQL](/assets/images/技术教程类/框架使用/MyBatis/MyBatis配置SQL注解提示/03-SelectMySQL.png "往下翻，找MySQL")  

然后在IDEA右边的`database`添加`Data Source`为`MySQL`
![添加数据源](/assets/images/技术教程类/框架使用/MyBatis/MyBatis配置SQL注解提示/04-AddDataSource.png "添加数据源")  
![填写数据库信息](/assets/images/技术教程类/框架使用/MyBatis/MyBatis配置SQL注解提示/05-DataSourceInformation.png "填写数据库信息")  
备注：个人学习的话，这里的数据库名填不填感觉都一样，AI建议填那我就填吧~~~   
（插图，可跳过）  
![要不要填数据库文件夹的名字](/assets/images/技术教程类/框架使用/MyBatis/MyBatis配置SQL注解提示/06-FillOutSchemaName.png "要不要填数据库文件夹的名字")  

现在可以愉快地写SQL了
![自动提示~~~](/assets/images/技术教程类/框架使用/MyBatis/MyBatis配置SQL注解提示/07-Success.png "自动提示~~~")  


### （2）情况2：设置了语言注入但不检查
我的IDEA默认完成了`Inject language or reference`，显示为`Uninject language or reference`。这种情况(`SQL dialect is not configured`)只会弹出SQL关键字、不会检查写错没有、不会弹出表名  
![已经注入了语言](/assets/images/技术教程类/框架使用/MyBatis/MyBatis配置SQL注解提示/08-UninjectLanguageOrReference.png "已经注入了语言")  

可以点击`Uninject language or reference`，然后按照`情况1`的步骤操作。或者按照根据编译器的提示设置`SQL Dialect`（方言）为`MySQL`

![没有配置SQL方言](/assets/images/技术教程类/框架使用/MyBatis/MyBatis配置SQL注解提示/09-SqlDialectIsNotConfigured.png "没有配置SQL方言")  
![点击SQL Dialect选择具体的SQL](/assets/images/技术教程类/框架使用/MyBatis/MyBatis配置SQL注解提示/10-ConfigureSqlDialect.png "点击SQL Dialect选择具体的SQL")  


### （3）情况3：设置了语言注入、SQL方言，还是不检查
右键注解内双引号包裹的SQL语句 → `Attach Data Source`即可
![连接数据源](/assets/images/技术教程类/框架使用/MyBatis/MyBatis配置SQL注解提示/11-AttachDataSource.png "连接数据源")  


### （4）其他情况
暂时没遇到了……

---
