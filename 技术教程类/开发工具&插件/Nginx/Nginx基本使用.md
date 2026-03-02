# Nginx基本使用

---

## 1、简介
> Nginx 是一款高性能的Web服务器和反向代理服务器。在前后端分离的项目中，它常被用作"网关":   
> * 反向代理：隐藏后端真实接口，统一访问入口  
> * 负载均衡：将流量分发到多个服务器  
> * 动静分离：高效处理图片、HTML 等静态资源

这里展示如何用Nginx代理请求路径: `http://localhost:8080/xxx`直接暴露后端端口，不安全，于是利用Nginx的代理功能，隐藏真实后端服务器的地址和端口


## 2、安装
进入<u>[官网](https://nginx.org/en/)</u> → 右侧菜单栏`download` → 选择`Stable Version`的`nginx/Windows-版本号`(假设系统是Windows)  
![](/assets/images/技术教程类/Nginx基本使用/01-OfficialWebsite.png "")  
![](/assets/images/技术教程类/Nginx基本使用/02-Download.png "")  
(`Mainline Version`是尝试新功能的版本)

解压后点击运行`nginx.exe`，有个黑屏幕一闪而过，然后`Ctrl + Shift + Esc`打开任务管理器看得到运行进程，说明运行成功  
![](/assets/images/技术教程类/Nginx基本使用/03-ContinueToRun.png "")  
![](/assets/images/技术教程类/Nginx基本使用/04-TaskManager.png "")  



## 3、基本使用
### (1)
在conf文件夹找到nginx.conf，里面有Nginx的默认端口号80，可以改成90，目的是避免冲突(其他应用也可能占用80端口号)  
![](/assets/images/技术教程类/Nginx基本使用/05-Config.png "")  
![](/assets/images/技术教程类/Nginx基本使用/06-ListenPort.png "")  
```Nginx
server {
    listen       90;        # Nginx 监听的端口
    server_name  localhost; # 访问域名

    ...
}
```


### (2)​
访问`http://localhost:90`（根据自己设定的`conf`端口号来访问），可以打开Nginx的欢迎界面​  
![](/assets/images/技术教程类/Nginx基本使用/07-HelloNginx.png "")  


### (3)
我按照AI给的方案一修改Nginx、SpringBoot配置，重新运行项目、Nginx(可以从任务管理器终止任务)，对于`http://localhost:8080/depts`对应的界面(展示所有的部门信息)，就可以通过`http://localhost:90/api/depts`来访问相同界面，只是链接被“中转”了一下，路径更规范安全  
![](/assets/images/技术教程类/Nginx基本使用/08-Solution1.png "")  
👆方案1-方案2👇  
![](/assets/images/技术教程类/Nginx基本使用/09-Solution2.png "")  

---

