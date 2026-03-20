# Node.js安装配置

---

# 1、简介
`Node.js`是一个`JavaScript`运行环境，这里用来快速创建vue项目

```java
AI简介
- 写后端服务（API、网站后台）
- 快速搭建前端项目（vue、react、next.js 等脚手架都靠它）
- 写工具脚本、构建工具（webpack、vite、eslint 基本都用 node 运行）
```



# 2、安装
## (1)下载
来到<u>[官网下载界面](https://nodejs.org/en)</u>，配置用默认的即可(`Windows、docker、npm、x64`)，主要是版本选择:

* 选长期支持版本(`LongTermSupported`)

* 选跟项目匹配/兼容的版本(因为我是学习阶段，就直接上了最新的LTS)

然后点击`Windows Installer (.msi)`下载安装器

![](/assets/images/技术教程类/运行环境/Node.js/Node.js安装配置/01-Download.png "")


## (2)安装
运行下载好的安装器，一路继续即可，保持默认选项(除了选择安装目录)

![](/assets/images/技术教程类/运行环境/Node.js/Node.js安装配置/02.png "")

![](/assets/images/技术教程类/运行环境/Node.js/Node.js安装配置/03.png "")

![](/assets/images/技术教程类/运行环境/Node.js/Node.js安装配置/04.png "")

![](/assets/images/技术教程类/运行环境/Node.js/Node.js安装配置/05.png "")

![](/assets/images/技术教程类/运行环境/Node.js/Node.js安装配置/06.png "")

![](/assets/images/技术教程类/运行环境/Node.js/Node.js安装配置/07.png "")

![](/assets/images/技术教程类/运行环境/Node.js/Node.js安装配置/08-FinishInstall.png "")


# 3、配置
## (1)验证环境变量
打开cmd命令行，分别输入以下命令，正常情况会输出版本号

```bash
:: Node.js 的版本号
node -v

:: Node Package Manager，是 Node.js 的软件包管理器，类似 Linux 的 apt( Advanced Pacage Tool )
npm -v
```


## (2)配置`npm`的全局安装路径
会让通过`npm`下载的东西，装在这个目录里面

```bash
:: 这个路径也可以不加双引号
npm config set prefix "Node.js的安装目录/自定义文件夹"

:: 切换为淘宝镜像，加速 npm 下载其他工具(npm install xxx)：
npm config set registry https://registry.npmmirror.com
```



# 3、创建vue项目
## (1)创建项目
打开cmd命令行

```bash
:: 切换到D盘
D:

:: 切换到放项目的文件夹
cd   目标目录

:: 只写 `vue` 默认最新版本，可以写 `vue@具体版本号` 来指定版本
npm create vue
```

* 如果第1次创建vue项目，它会安装`create-vue`项目脚手架工具(通常在 `C:\Users\用户名\AppData\Local\npm-cache`里面)，可以帮助生成一个标准的 Vue 项目模板

* 创建项目时，项目名称可以按tab键直接变成它默认的`vue-project`，或者自己输入

* 创建项目的其他选项默认即可

* 创建项目(需要安装`create-vue`)、安装项目依赖**都需要联网**

![](/assets/images/技术教程类/运行环境/Node.js/Node.js安装配置/09-CreateVue.png "")

然后进入项目所在目录，安装项目依赖


## (2)安装项目依赖
```bash
:: 切换到项目所在目录
cd  vue-project01

:: 安装项目依赖
npm install
```

![](/assets/images/技术教程类/运行环境/Node.js/Node.js安装配置/10-InstallProjectDependency.png "")


## (3)命令行打开项目
输入`npm run dev`然后 Ctrl + 单击链接

![](/assets/images/技术教程类/运行环境/Node.js/Node.js安装配置/11-OpenInCmd.png "")


## (4)IDEA打开项目
找到src文件夹下的`pacage.json`，然后点击绿色三角形运行

![](/assets/images/技术教程类/运行环境/Node.js/Node.js安装配置/12-OpenInIdea.png "")

发现需要填表

`Node interpreter:`点击右边的3个点，选到`Node.js`安装目录里面的`node.exe`，`Package manager:`是同目录下的`npm.cmd`(填好`Node interpreter:`后可以自动填好这里)

![](/assets/images/技术教程类/运行环境/Node.js/Node.js安装配置/13.png "")

![](/assets/images/技术教程类/运行环境/Node.js/Node.js安装配置/14.png "")

![](/assets/images/技术教程类/运行环境/Node.js/Node.js安装配置/15.png "")

填好就可以运行、从IDEA控制台打开链接

![](/assets/images/技术教程类/运行环境/Node.js/Node.js安装配置/16.png "")

![](/assets/images/技术教程类/运行环境/Node.js/Node.js安装配置/17-HelloWorld.png "")

当然还需要配置IDEA全局设置，防止之后还需要填表
`Settings` → `Languages & Frameworks` → `Node.js`，跟刚才填表的一样

![](/assets/images/技术教程类/运行环境/Node.js/Node.js安装配置/18.png "")

![](/assets/images/技术教程类/运行环境/Node.js/Node.js安装配置/19.png "")

---

> **往期文章**  
> <u>[开通阿里云对象存储服务](/技术教程类/云服务/对象存储服务/开通阿里云对象存储服务.md)</u>  
> <u>[PageHelper简介](/技术教程类/开发工具&插件/PageHelper/PageHelper简介.md)</u>  
> <u>[IDEAMaven项目打包](/技术教程类/项目打包/IDEAMaven项目打包.md)</u>  
> <u>[MyBatis驼峰转换](/问题排查/MyBatis驼峰转换.md)</u>  
> <u>[MyBatis基础学习笔记](/技术教程类/框架使用/MyBatis/MyBatis基础学习笔记.md)</u>  
> <u>[MyBatis配置Mapper.xml模板](/技术教程类/框架使用/MyBatis/MyBatis配置Mapper.xml模板.md)</u>  
> <u>[MyBatis配置SQL注解提示](/技术教程类/框架使用/MyBatis/MyBatis配置SQL注解提示.md)</u>  
> <u>[一道SQL分组过滤题](/数据结构与算法刷题/SQL/一道SQL分组过滤题.md)</u>  
> <u>[试一下手写having关键字](/技术探索&评测/试一下手写having关键字.md)</u>  
> ……  

