# Node.js切换版本

---

---

# 1、背景描述
部分项目的依赖不支持高版本Node.js，如果把这个依赖切换为同类型其他依赖，可能需要改动多处语法，比较麻烦，于是考虑降低Node版本。于是就需要配置`Node Version Manager`



# 2、步骤
## (1)删除已有Node

**①npm缓存**

`C:\Users\用户名\AppData\Local\npm-cache`，这个是旧 Node.js 遗留的缓存文件夹，里面存的是之前下载过的包等等,对新安装的NVM没有影响、并且NVM的Node也用这个文件夹作为npm缓存，**可删可不删**

如果想要清理一下，可以命令行`npm cache clean --force`删掉`_caches`文件夹

如果想要清空，就管理员身份运行cmd

```bash
:: 清除缓存文件夹
rmdir /s  "C:\Users\自己的用户名\AppData\Local\npm-cache"
```

不过因为权限问题，会留下1个`_logs`日志文件夹删不掉，不用管

**②删App**

Win + I → `应用` → `安装的应用` → `卸载`，这里会删掉Node文件夹里面的东西

![](/assets/images/技术教程类/运行环境/Node.js/Node.js切换版本/01-UninstallNode.png "")

出现如图这种情况是有Node进程还没有关闭，直接点击`OK`让它自动结束进程即可

![](/assets/images/技术教程类/运行环境/Node.js/Node.js切换版本/02.png "")

也可以`Ctrl + Shift + Esc`打开任务管理器，手动结束Node相关进程

![](/assets/images/技术教程类/运行环境/Node.js/Node.js切换版本/03.png "")

**③检验**

* 之前放Node的文件夹，里面的东西没了
* 并且命令行输入`node -v`、`npm -v`没反应
* cmd输入`path`或者`echo.%path:;= & echo.%`(这个有换行分隔更好看)，发现原来的Node环境变量没了
* 如果是PowerShell就输入`$env:path`或者带换行的`$env:path -split ';'`
* 于是就删除成功了


## (2)下载NVM安装器
来到<u>[NVM的GitHub Release界面](https://github.com/coreybutler/nvm-windows/releases)</u>，最新版即可，看到`Assets`，下载`nvm-setup.exe`安装器(或者`nvm-setup.zip`再解压)，然后点击运行安装器


## (3)安装NVM

![](/assets/images/技术教程类/运行环境/Node.js/Node.js切换版本/04-StartInstall.png "")

设置NVM安装位置

![](/assets/images/技术教程类/运行环境/Node.js/Node.js切换版本/05-NvmLocation.png "")

设置`Active Version Location（活跃版本位置）`，这个文件夹也叫做`符号链接（Symlink）目录`，指向NVM当前使用的Node版本文件夹。可以设置为D盘里面新建的**空的**`nodejs`文件夹

![](/assets/images/技术教程类/运行环境/Node.js/Node.js切换版本/06-NodejsLocation.png "")

![](/assets/images/技术教程类/运行环境/Node.js/Node.js切换版本/07.png "")

![](/assets/images/技术教程类/运行环境/Node.js/Node.js切换版本/08.png "")

![](/assets/images/技术教程类/运行环境/Node.js/Node.js切换版本/09.png "")

![](/assets/images/技术教程类/运行环境/Node.js/Node.js切换版本/10.png "")

![](/assets/images/技术教程类/运行环境/Node.js/Node.js切换版本/11.png "")

命令行`nvm -v`有反应，安装成功

## (4)配置Node

```bash
:: 安装 Node16 的最新版
nvm install 16

:: 删除
nvm uninstall 16

:: 指定具体的版本号16.20.2会更准确一点，不过通常一个主版本号只安装 1个Node，所以不用担心
:: 切换版本
nvm use 16

:: 查看所有的Node版本(前面带了星号的，表示当前正在使用)
nvm list

:: 查看版本
node -v
npm -v

:: 查看环境变量有没有变化
path

:: 切换镜像源，加速国内网络下载
nvm node_mirror https://npmmirror.com/mirrors/node/
nvm npm_mirror https://npmmirror.com/mirrors/npm/
```


## (5)注意Node <= 15的版本不能通过NVM下载

**①下载Node.js整体**

去<u>[Node.js官网下载界面](https://nodejs.org/en/download)</u>，选到目标版本，点击`Standalone Binary (.zip)`下载压缩包

![](/assets/images/技术教程类/运行环境/Node.js/Node.js切换版本/12.png "")

解压后把文件夹的名字只保留版本号，塞到NVM安装文件夹里面

![](/assets/images/技术教程类/运行环境/Node.js/Node.js切换版本/13.png "")

然后重启cmd，`nvm list`应该可以看到刚刚塞进来的版本

**②分开下载Node本体、npm工具包**

这个是我弄麻烦了，直接用方法①就好了，但是当时没想到

比如配置Node14.21.3👇

1. 我先去的 <u>https://registry.npmmirror.com/binary.html?path=node/v14.21.3/win-x64/</u> 下载node.exe

2. 再去 <u>https://registry.npmmirror.com/binary.html?path=npm/v6.14.18/</u> 下载匹配版本的npm压缩包

3. 打开NVM文件夹，创建`v14.21.3`文件夹，把node.exe放进去

4. 在`v14.21.3`文件夹里面创建`node_modules`文件夹

5. 把npm压缩包解压，里面的文件夹(可能叫做`cli-6.14.18`或者类似名字)重命名为`npm`，放进`node_modules`文件夹

6. 把`npm`文件夹里面的`bin`文件夹的`npm`、`npm.cmd`、`npx`、`npx.cmd`四个文件复制，粘贴到 `v14.21.3` 文件夹里面（和 `node.exe` 同一级）

7. 再`nvm list`就出现`14.21.3`了

8. 对照NVM文件夹的其他版本Node，可以发现大家的结构都差不多

---

> **往期文章**  
> <u>[Node.js安装配置](/技术教程类/运行环境/Node.js/Node.js安装配置.md)</u>  
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
