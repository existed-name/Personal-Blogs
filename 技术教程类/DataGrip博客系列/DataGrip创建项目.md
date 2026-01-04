# DataGrip创建项目

> 上一期：[DataGrip安装](/技术教程类/DataGrip博客系列/DataGrip安装.md)  
> 参考文章：[DataGrip安装文档](https://heuqqdmbyk.feishu.cn/wiki/FAa3wj0nYi4xGBksbFuchBK8nhe#STS5dq9hioTBPGxK6Cpc2NA1nff)
---

1. DataGrip 主页 → 点击 `New Project` 创建项目
   ![主页](/assets/images/技术教程类/DataGrip博客系列/Homepage.png)
   ![新建项目](/assets/images/技术教程类/DataGrip博客系列/DataGrip创建项目/NewProject.png)
2. 项目存放位置
   1. 默认放在 `C:\Users\用户名\DataGripProjects` 文件夹下
      ![默认位置](/assets/images/技术教程类/DataGrip博客系列/DataGrip创建项目/ProjectsDefaultDirctory.png)
   2. 可以在 `New Project` 时选择位置
   3. 如果创建到了默认位置，可以先移除项目，把项目文件夹移到 D 盘，再用 DataGrip 导入项目  
      ![移除项目](/assets/images/技术教程类/DataGrip博客系列/DataGrip创建项目/RemoveProject.png)  
3. 选择数据库类型：左边 `Database Explorer` 栏 → 点击`+` → `Data Source` → `MySQL`（这里用的 MySQL ）  
   ![SelectDataSource](/assets/images/技术教程类/DataGrip博客系列/DataGrip创建项目/SelectDataSource.png)  
4. 连接数据库
   1. **需要提前打开 `MySQL` 服务**：管理员身份运行 cmd → 输入 `net start mysql`
      ![启动 MySQL](/assets/images/技术教程类/DataGrip博客系列/DataGrip创建项目/StartMysqlService.png "启动 MySQL" )  
   2. 在 DataGrip 中输入用户名、密码
      ![输入用户名和密码](/assets/images/技术教程类/DataGrip博客系列/DataGrip创建项目/CreateConnection.png "输入用户名和密码" )  
   3. 点击右下角 `Test Connection` 测试连接
      1. 提示下载驱动文件
         ![测试失败](/assets/images/技术教程类/DataGrip博客系列/DataGrip创建项目/TestConnection-Failed.png "测试失败" )
      2. 于是下载( `Download missing driver files` )
         ![下载驱动](/assets/images/技术教程类/DataGrip博客系列/DataGrip创建项目/DownloadDriverFiles.png "下载驱动" )
      3. 再次测试
         ![测试成功](/assets/images/技术教程类/DataGrip博客系列/DataGrip创建项目/NewProject.png "测试成功" )
      4. 通过测试后，就可以 `Apply` 了
5. 展示数据库文件夹
   ![图1](/assets/images/技术教程类/DataGrip博客系列/DataGrip创建项目/DisplaySchema1.png "图1" )  
   ![图2](/assets/images/技术教程类/DataGrip博客系列/DataGrip创建项目/DisplaySchema2.png "图2" )  
   ![图3](/assets/images/技术教程类/DataGrip博客系列/DataGrip创建项目/DisplaySchema3.png "图3" )  
6. 右上角选择要写入数据的文件夹
   ![选择数据库](/assets/images/技术教程类/DataGrip博客系列/DataGrip创建项目/SelectDatabase.png "选择数据库" )
7. 在控制台写完代码点击运行
   ![控制台](/assets/images/技术教程类/DataGrip博客系列/DataGrip创建项目/Console.png "控制台" )  
8. 从左侧文件目录打开创建的表
   1. 添加的数据在点击 `submit` ( 那个 `↑` )后编译器才会检查
   2. 如果修改、重新运行代码，表格可能需要刷新才会生效/展示新表格
      ![数据表图](/assets/images/技术教程类/DataGrip博客系列/DataGrip创建项目/Table.png "数据表图" )



