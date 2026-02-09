# 配置`WiX Toolset`


---

## 1. 背景
打`JPackage`，首先需要安装 `WiX Toolset` 打包引擎，否则 `jpackage` 会报错找不到打包环境
```bash
找不到 WiX工具(light.exe，candle.exe)
从 https://wixtoolset.org 下载 wiX 3.0 或更高版本，然后将其添加到 PATH。
```


## 2. 安装`WiX Toolset`
来到 <u>[WiX Toolset3 发布界面](https://github.com/wixtoolset/wix3/releases)</u>(注意GitHub会搜到更高版本，但是推荐v3)，下载`.exe`安装器  
![发布界面](/assets/images/技术教程类/项目打包/配置WiXToolset/01-Release.png "发布界面")  
![启动安装器](/assets/images/技术教程类/项目打包/配置WiXToolset/02-Installer.png "启动安装器")  

启动安装器，点击弹出的界面的`install`选项，等待安装完成  
![点击install](/assets/images/技术教程类/项目打包/配置WiXToolset/03-Install.png "点击install")  

它没有安装目录选项，直接安装到`C:\Program Files (x86)\WiX Toolset v3.14`里面，但是**可以手动移到D盘**


## 3. 配置环境变量
`Win + I`打开设置 → `系统` → 向下翻`系统信息` → `设备规格`那里有个`相关链接` → `高级系统设置`  
![点击“高级系统设置”](/assets/images/技术教程类/项目打包/配置WiXToolset/04-SystemInformation.png "点击“高级系统设置”")  

然后新建系统变量`WIX_HOME`，把`Wix Toolset`文件夹所在地址复制进去  
![系统属性-高级-环境变量](/assets/images/技术教程类/项目打包/配置WiXToolset/05-EnvironmentVariable.png "系统属性-高级-环境变量")  
![新建系统变量](/assets/images/技术教程类/项目打包/配置WiXToolset/06-CreateSystemVairable.png "新建系统变量")  
![添加变量名和路径](/assets/images/技术教程类/项目打包/配置WiXToolset/07-WixHome.png "添加变量名和路径")  

再双击进入`Path`，新建环境变量`%WIX HOME%\bin`  
![新建环境变量](/assets/images/技术教程类/项目打包/配置WiXToolset/08-EditEnvironmentVar.png "新建环境变量")  

然后一路**点击确定、打开新的命令行**，确保环境变量生效


## 4. 验证环境变量
命令行输入`candle -?`、`light -?`都可以输出详细信息  
![验证配置成功](/assets/images/技术教程类/项目打包/配置WiXToolset/09-Verify.png "验证配置成功")  


## 5. 附
有可能已经配置好，但还是报错找不到环境变量: 
* 重开新的命令行
* 如果还没好，重新打开设置、进入环境变量那里面，只需要一路点`确定`，然后重开命令行

---

