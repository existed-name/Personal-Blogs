# DataGrip不能展示新建的数据库

---

## （一）问题描述：
1. DataGrip创建项目后，连接的本地MySQL数据库

2. 之后在cmd上创建了一个数据库文件夹（`create database`），默认放在MySQL安装目录的`data`文件夹里面

3. 进入DataGrip后，发现左侧数据库展示栏（`Database Explorer`）并没有展示新创建的数据库文件夹

4. 点击左上的`Refresh`、重启DataGrip也不显示  
![刷新展示栏](/assets/images/问题排查/DataGrip不能展示新建的数据库/RefreshDatabaseExplorer.png "刷新展示栏")


## （二）解决方法：
1. 回顾教程的DataGrip连接数据库，注意到连接成功后需要选择要展示哪些数据库

2. 于是猜想可能是新建的数据库没有展示出来

3. 点击`@localhost`右边的数字，观察可以选择展示的数据库，然而没有看到新建的数据库  
![进入选择展示列表](/assets/images/问题排查/DataGrip不能展示新建的数据库/EnterDisplayList.png "进入选择展示列表")

4. 离谱的是，这个“选择展示列表”也可以刷新，刷新之后就看到那个新建的数据库了  
![刷新列表](/assets/images/问题排查/DataGrip不能展示新建的数据库/RefreshDisplayList.png "刷新列表")  
![成功展示](/assets/images/问题排查/DataGrip不能展示新建的数据库/Success.png "成功展示")  

5. 还有其他的bug：打开DataGrip时，用cmd创建`test_database`，DataGrip可以自动展示这个新建的数据库，但是cmd删掉这个数据库后，点击`Refresh`没有反应、得刷新“选择展示列表”才行


## （三）总结
1. 这些bug不一定成立，可能偶尔才会遇到，又试了几次创建删除数据库都可以正常显示

2. 遇到DataGrip相关的展示问题，首先考虑刷新：
	1. 表可以`Reload Page`  
		![刷新表格](/assets/images/问题排查/DataGrip不能展示新建的数据库/ReloadPage.png "刷新表格")  

	2. 数据库可以`Refresh`

	3. 还可以重启

---

