# FinalShell连接虚拟机

## 目录
- [1、简介](#1简介)
- [2、下载安装](#2下载安装)
- [3、虚拟机网络设置](#3虚拟机网络设置)
- [4、启动FinalShell](#4启动finalshell)
- [5、附](#5附)

---

---


# 1、简介
* FinalShell是一款SSH(`Secure Shell`)远程连接客户端软件，这里用来连接虚拟机上传文件
* 虚拟机软件: VirtualBox7.2.4
* 虚拟机系统: Debian13.1.0



# 2、下载安装
1. 来到官网：<u>https://www.hostbuf.com/t/988.html</u>，下载`Windows X64版`,拿到1个`finalshell_windows_x64.exe`

    ![](/assets/images/技术教程类/项目部署/FinalShell连接虚拟机/01-Download.png "")

2. 启动`.exe`安装器

    一路确定
    
    ![](/assets/images/技术教程类/项目部署/FinalShell连接虚拟机/02-StartInstall.png "")
    
    ![](/assets/images/技术教程类/项目部署/FinalShell连接虚拟机/03.png "")
    
    选择安装目录
    
    ![](/assets/images/技术教程类/项目部署/FinalShell连接虚拟机/04-InstallDirectory.png "")
    
    ![](/assets/images/技术教程类/项目部署/FinalShell连接虚拟机/05.png "")

3. 安装成功

    右上角可以设置通过FinalShell下载的文件的放置目录
    
    ![](/assets/images/技术教程类/项目部署/FinalShell连接虚拟机/06.png "")



# 3、虚拟机网络设置
1. VirtualBox左侧导航栏 → 网络 → 进去后看到上面部分，选择`NAT网络`,然后点击创建

2. 通用选项
    * 名称，比如`MyNatNetwork`
    * Ipv4网络掩码: `192.168.100.0/24`(网络地址/子网掩码)
    * 启用DHCP
    * 关闭IPv6

    ![](/assets/images/技术教程类/项目部署/FinalShell连接虚拟机/07-VirtualBoxNetworkSetting.png "")

3. 左侧导航栏进入`Machines`，选中自己的虚拟机，点击设置

   ![](/assets/images/技术教程类/项目部署/FinalShell连接虚拟机/08.png "")

4. 来到网络设置
    * Attached to: NAT网络
    * Name: 刚刚创建的`NAT网络`(`MyNatNetwork`)
    * Adapter Type: 默认(注：虚拟机启动的时候不可修改)
    * `Promiscuous Mode`: 默认
    * MAC Address: 默认(注：虚拟机启动的时候不可修改)
    * 开启`Virtual Cable Connected`

    ![](/assets/images/技术教程类/项目部署/FinalShell连接虚拟机/09-VMNetworkSetting.png "")

5. 启动虚拟机，登录
   * `Ctrl + Alt + T`打开命令行，输入`ip addr show`查看当前虚拟机分配到的IP
   * 找到类似`enp0s3`或`eth0`部分，看到下面部分的`inet`红色数字，我的是`192.168.100.3`，把它记录下来

   ![](/assets/images/技术教程类/项目部署/FinalShell连接虚拟机/10-ShowIp.png "")

6. 回到VirtualBox的网络设置 → NAT网络 → 选中刚刚创建的 → 端口转发
    * VirtualBox的虚拟机不可以直接访问，需要本地电脑中转一下

7. 添加端口转发规则
    * 名称: 比如`SSH`，用作FinalShell等等SSH工具连接虚拟机时的端口转发规则
    * 协议: `TCP`
    * 主机IP: 不用填，或者127.0.0.1，都指向自己的电脑
    * 主机端口: 2222,主机监听的端口，FinalShell访问这个端口、主机再转发给虚拟机
    * 子系统/客户机IP: 也就是我们要访问的虚拟机IP，**填自己的**，`192.168.100.3`
    * 子系统/客户机端口: 22(SSH默认端口)，虚拟机监听的端口，由主机监听FinalShell后转过来

    ![](/assets/images/技术教程类/项目部署/FinalShell连接虚拟机/11-PortForwarding.png "")

8. 关闭虚拟机，从VirtualBox重新启动，让配置生效
    * 虚拟机命令行`sudo reboot`重启好像并不能让端口转发生效，还是要先退出，再打开



# 4、启动FinalShell
1. 创建SSH连接

    ![](/assets/images/技术教程类/项目部署/FinalShell连接虚拟机/12-CreateSshConnection.png "")

2. 设置SSH连接规则
    * 名称: 随便填，为了区分可以填虚拟机IP
    * 主机: 127.0.0.1(localhost)
    * 端口: 主机监听的端口，填我们在VirtualBox里面设置的`主机端口`2222
    * 认证: 用户名 + 密码
    * 应用、确定

   ![](/assets/images/技术教程类/项目部署/FinalShell连接虚拟机/13.png "")

3. 连接成功
    * 这里选`接受并保存`, `只接受本次`会连接中断

   ![](/assets/images/技术教程类/项目部署/FinalShell连接虚拟机/14.png "")

    * 可以从电脑把文件拖进来直接上传，注意**上传位置在当前选中的虚拟机文件夹**
   ![](/assets/images/技术教程类/项目部署/FinalShell连接虚拟机/15-ConnectSuccess.png "")



# 5、附
1. 连接超时通常是配置问题
    * 确保VirtualBox的端口转发子系统/客户机IP = 虚拟机的IP
    * 端口转发`主机端口` = FinalShell连接设置的端口
    * 虚拟机偶尔会抽风，每次启动都需要`ip addr show`检查有没有出现预期的`192.168.100.xxx`，没有的话就退出、重新打开
    * 好像VMware更简单一点，以后再试一下

2. FinalShell上传文件失败
    * 很可能是目标文件夹的权限不够，可以用AI帮着设定权限
    * 小部分自己的问题，关闭重启FinalShell

---

> **往期文章**
> <u>[虚拟机和主机共享剪贴板](https://zhuanlan.zhihu.com/p/1979949196330176615)</u>  
> <u>[Debian重置密码](https://zhuanlan.zhihu.com/p/1969045955505546935)</u>  
> <u>[Debian基本使用](https://zhuanlan.zhihu.com/p/1969068606273848129)</u>  
> <u>[Debian修改主机名](https://zhuanlan.zhihu.com/p/1969072103551631565)</u>  
> <u>[VirtualBox创建虚拟机](https://zhuanlan.zhihu.com/p/1968948685741225905)</u>  
> <u>[解决VirtualBox安装目录无效的问题](https://zhuanlan.zhihu.com/p/1968693323565863053)</u>  
> <u>[安装VirtualBox7.2.4](https://zhuanlan.zhihu.com/p/1968673825353863573)</u>  
> **其他推荐**
> <u>[解决Gemini不支持国家](https://www.zhihu.com/question/1936843079798749071/answer/2007801005392273588)</u>  
> <u>[软工.大二上.学期总结](https://zhuanlan.zhihu.com/p/2013893281885463863)</u>  
> <u>[读书笔记专栏](https://www.zhihu.com/column/c_1991579880740110802)</u>  
> <u>[Node.js切换版本](https://zhuanlan.zhihu.com/p/2027012596826416513)</u>  