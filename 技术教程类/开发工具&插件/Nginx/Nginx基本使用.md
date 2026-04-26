# Nginx基本使用

---

> 4.26更新环境变量配置、命令行脚本

# 1、简介
> Nginx 是一款高性能的Web服务器和反向代理服务器。在前后端分离的项目中，它常被用作"网关":   
> * 反向代理：隐藏后端真实接口，统一访问入口  
> * 负载均衡：将流量分发到多个服务器  
> * 动静分离：高效处理图片、HTML 等静态资源

这里展示如何用Nginx代理请求路径: `localhost:8080/xxx`直接暴露后端端口，不安全，于是利用Nginx的代理功能，隐藏真实后端服务器的地址和端口


# 2、安装
进入<u>[官网](https://nginx.org/en/)</u> → 右侧菜单栏`download` → 选择`Stable Version`的`nginx/Windows-版本号`(假设系统是Windows)  
![](/assets/images/技术教程类/开发工具&插件/Nginx/Nginx基本使用/01-OfficialWebsite.png "")  
![](/assets/images/技术教程类/开发工具&插件/Nginx/Nginx基本使用/02-Download.png "")  
(`Mainline Version`是尝试新功能的版本)

解压后点击运行`nginx.exe`，有个黑屏幕一闪而过，然后`Ctrl + Shift + Esc`打开任务管理器看得到运行进程，说明运行成功  
![](/assets/images/技术教程类/开发工具&插件/Nginx/Nginx基本使用/03-ContinueToRun.png "")  
![](/assets/images/技术教程类/开发工具&插件/Nginx/Nginx基本使用/04-TaskManager.png "")  



# 3、基本使用
## (1)
在conf文件夹找到nginx.conf，里面有Nginx的默认端口号80，可以改成90，目的是避免冲突(其他应用也可能占用80端口号)  
![](/assets/images/技术教程类/开发工具&插件/Nginx/Nginx基本使用/05-Config.png "")  
![](/assets/images/技术教程类/开发工具&插件/Nginx/Nginx基本使用/06-ListenPort.png "")  
```Nginx
server {
    listen       90;        # Nginx 监听的端口
    server_name  localhost; # 访问域名

    ...
}
```


## (2)​
访问`http://localhost:90`（根据自己设定的`conf`端口号来访问），可以打开Nginx的欢迎界面​  
![](/assets/images/技术教程类/开发工具&插件/Nginx/Nginx基本使用/07-HelloNginx.png "")  


## (3)
我按照AI给的方案一修改Nginx、SpringBoot配置，重新运行项目、Nginx(可以从任务管理器终止任务)，对于`http://localhost:8080/depts`对应的界面(展示所有的部门信息)，就可以通过`http://localhost:90/api/depts`来访问相同界面，只是链接被“中转”了一下，路径更规范安全  
![](/assets/images/技术教程类/开发工具&插件/Nginx/Nginx基本使用/08-Solution1.png "")  
👆方案1-方案2👇  
![](/assets/images/技术教程类/开发工具&插件/Nginx/Nginx基本使用/09-Solution2.png "")  



# 4、环境变量
## (1)配置
* 注意**先关闭命令行、关闭已打开的nginx进程**

1. 电脑设置 → 系统 → 系统信息 → 高级系统设置 → 环境变量

    ![](/assets/images/技术教程类/开发工具&插件/Nginx/Nginx基本使用/10.png "")

2. 新建系统变量 → 名称`NGINX_HOME` → 路径为nginx的安装文件夹目录

    ![](/assets/images/技术教程类/开发工具&插件/Nginx/Nginx基本使用/11.png "")

3. 双击`系统变量`里面的`Path` → 新建 → `%NGINX_HOME%`

4. 一路确定，让环境变量生效

5. 打开cmd输入`nignx -v`验证


## (2)基础命令
* **首先需要切换到nginx安装文件夹执行命令**，否则会警告路径错误/找不到文件，因为nginx的命令针对的当前所在目录

```shell
# 1. 启动 Nginx
nginx

# 2. 检查配置语法（必须先执行，无 error 再操作）
nginx -t

# 3. 优雅重载配置（修改 nginx.conf 后用）
nginx -s reload

# 4. 快速停止（优雅关闭，处理完当前请求）
nginx -s quit

# 5. 强制停止（立即 kill 所有进程）
nginx -s stop

# 6. 显示版本和编译信息
nginx -v
nginx -V   # 更详细（包含模块等）

# 7. 指定配置文件启动（特殊情况用）
nginx -c D:/nginx/conf/nginx.conf
```


## (3)脚本
* 这里是让AI写的花里胡哨的命令行脚本
* 可以复制下来、修改里面的nginx目录、保存为`.txt`、修改文件后缀为`.bat`、放在nginx文件夹里面，然后运行脚本代替手动命令
* 注意尽量不带中文标点、2行汉字之间尽量换行，避免乱码

1. `nginx-start.bat`(启动)

```shell
@echo off
chcp 65001 >nul
title Nginx 启动

echo.
echo ================================================
echo               Nginx 启动工具
echo ================================================
echo.

echo [1/3] 切换到 Nginx 目录...
cd /d D:\Users\Programming\Web-Server\Nginx\nginx-1.28.2

echo [2/3] 检查配置文件语法...
nginx -t
if errorlevel 1 (
    echo.
    echo [错误] 配置检查失败！请检查 nginx.conf
    echo.
    pause
    exit /b 1
)

echo [3/3] 启动 Nginx 到后台...
:: nginx  或者  nginx -g "daemon on;"  都会卡住cmd(不立即返回提示符,但是nginx已经运行)
:: 使用 start /b 彻底后台运行，不会卡住当前命令行
start /b nginx -g "daemon on;"

echo.
echo ================================================
echo [成功] Nginx 已启动到后台运行!
echo 端口 90 可正常访问
echo ================================================
echo.
echo 提示：修改配置后请双击 nginx-reload.bat 重载
echo.
pause
```

2. `nginx-stop.bat`(停止)

```shell
@echo off
chcp 65001 >nul
title Nginx 停止服务

echo.
echo ================================================
echo               Nginx 停止服务
echo ================================================
echo.

echo [1/1] 切换到 Nginx 目录并停止服务...
cd /d D:\Users\Programming\Web-Server\Nginx\nginx-1.28.2

nginx -s quit

echo.
echo ================================================
echo [成功] Nginx 已优雅停止!
echo 所有 nginx.exe 进程已关闭
echo ================================================
echo.
pause
```

3.`nginx-reload.bat`(重载配置)

```shell
@echo off
chcp 65001 >nul
title Nginx 重载配置

echo.
echo ================================================
echo               Nginx 配置重载
echo ================================================
echo.

echo [1/2] 切换到 Nginx 目录...
cd /d D:\Users\Programming\Web-Server\Nginx\nginx-1.28.2

echo [2/2] 检查并重载配置...
nginx -t
if errorlevel 1 (
    echo.
    echo [错误] 配置检查失败！请检查 nginx.conf
    echo.
    pause
    exit /b 1
)

nginx -s reload

echo.
echo ================================================
echo [成功] 配置已重载!
echo Nginx 已应用最新配置
echo ================================================
echo.
pause
```

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
