# 解决WindowsXP忘记密码


---

## 1、背景
家里有台远古WinXP台式电脑，开机后有个选择操作系统的环节: 
* `Microsoft Windows XP Professional`
* `一键GHOST v2011.07.01`

家里人误选了`一键ghost`恢复系统，然后登录密码一直错误

这里记录修改电脑密码的步骤



## 2、准备
### (1)工具
另一台可以用的电脑、U盘(至少512MB)


### (2)下载工具
首先需要下载WinPE工具(`Windows Preinstallation Environment`预安装环境)，听说<u>[老毛桃](https://www.laomaotao.net/)</u>近几年有捆绑软件，就用的AI推荐的<u>[微PE工具箱](https://www.wepe.com.cn/)</u>

进入<u>[官网](https://www.wepe.com.cn/)</u>，因为目标是WinXP，所以下载的v1.3的32位版本
![](/assets/images/问题排查/操作系统与硬件/Windows/解决WindowsXP忘记密码/01-DownloadWepe.png "")  


### (3)制作启动盘
**提前备份U盘数据，这一步会格式化U盘**

运行刚才下载的`WePE_32_V1.3.exe` → 右下角选择`安装PE到U盘`
![](/assets/images/问题排查/操作系统与硬件/Windows/解决WindowsXP忘记密码/02-DownloadToUDist.png "")  

然后设置属性(备注: `卷标` = 这个U盘的名字)
![](/assets/images/问题排查/操作系统与硬件/Windows/解决WindowsXP忘记密码/03-InstallSettings.png "")  
![](/assets/images/问题排查/操作系统与硬件/Windows/解决WindowsXP忘记密码/04-InstallSettingsExplanation.png "")  
![](/assets/images/问题排查/操作系统与硬件/Windows/解决WindowsXP忘记密码/05-FinishInstall.png "")  


### (4)检查是否制作成功
> 可以跳过这步~~~
1. U盘本身看不出什么东西——都被隐藏了

2. 不过右键U盘 → `属性` → 可以看到U盘`已用空间`

3. 然后`win`打开菜单(或者`Win + I`打开设置) → 搜索`磁盘管理` → 可以看到U盘分成了3个区
![](/assets/images/问题排查/操作系统与硬件/Windows/解决WindowsXP忘记密码/06-DiskManagement.png "")  
![](/assets/images/问题排查/操作系统与硬件/Windows/解决WindowsXP忘记密码/07-DiskPartition.png "")  

4. 当然，之后一插电脑，自然知道成没成功


### (5)下载密码修改工具
这里用的<u>[NTPWEdit](http://www.cdslow.org.ru/en/ntpwedit/)</u>，进去之后找到`DOWNLOAD`下载压缩包，把里面的`ntpwedit.exe`放进U盘(另一个`ntpwedit64.exe`是64位系统的)
![](/assets/images/问题排查/操作系统与硬件/Windows/解决WindowsXP忘记密码/08-DownloadNtpwedit.png "")  



## 3、操作
### (1)设置WinXP电脑的BIOS(基本输入/输出系统)
1. 插上U盘，开机，显示屏第1次出现画面后，马上按`Delete`打开BIOS设置界面
![](/assets/images/问题排查/操作系统与硬件/Windows/解决WindowsXP忘记密码/09-BIOS.png "")  

2. 这玩意儿没有光标，只能上下左右方向键来移动，Enter确定选择

3. 右移到`Advanced`目录
* 可以在`Mass Storage Devices`看到自己的U盘
* 然后确保`Legacy USB Support`是`Enabled`
![](/assets/images/问题排查/操作系统与硬件/Windows/解决WindowsXP忘记密码/10-Advanced.png "")  

4. 右移到`Boot`目录
* `UEFI Boot`设为`Disabled`
* `Boot Option Properties`的`Boot Option #1`改成U盘的名字
* 如果`Boot Option #1`没看到自己的U盘，就进入`Hard Drive BBS Priorities`，可以看到自己的U盘，把它设成`#1`，再设置`Boot Option Properties`
* 如果看到两个U盘选项（带UEFI前缀和不带的），选不带UEFI的那个（Legacy模式）
![](/assets/images/问题排查/操作系统与硬件/Windows/解决WindowsXP忘记密码/11-Boot.png "")  

5. 按F10保存退出、重启


### (2)改密码
1. 此时应该在加载微PE系统了
![](/assets/images/问题排查/操作系统与硬件/Windows/解决WindowsXP忘记密码/12-Loading1.png "")  
![](/assets/images/问题排查/操作系统与硬件/Windows/解决WindowsXP忘记密码/13-Loading2.png "")  
![](/assets/images/问题排查/操作系统与硬件/Windows/解决WindowsXP忘记密码/14-HomePage.png "")  

2. 打开密码修改工具
* `Win + R`输入`cmd`打开命令行，或者`Win`菜单栏 → `附件工具` → `命令提示符`(当前目录是U盘里面的微PE系统)

* 输入U盘对应的盘符切换目录，比如我是`G:`，然后输入`ntpwedit.exe`启动密码修改工具

* 也可以直接文件资源管理器进去打开程序
![](/assets/images/问题排查/操作系统与硬件/Windows/解决WindowsXP忘记密码/15-StartNtpwedit.png "")  


3. 输入SAM文件的绝对路径
* 可以`Win`打开菜单栏 → `文件工具` → `文件快速搜索`(`Everything`) → 搜索`SAM`找到他，这个文件存的是电脑密码，在系统运行时不可以打开
* WinXP的SAM路径一般是`C:\WINDOWS\SYSTEM32\CONFIG\SAM`


4. 点击`Open` → 选中目标用户(`Administrator`) → `Change password` → 然后修改密码(可以直接留空) → `OK` → `Save changes`
![](/assets/images/问题排查/操作系统与硬件/Windows/解决WindowsXP忘记密码/16-ChangePassword.png "")  
![](/assets/images/问题排查/操作系统与硬件/Windows/解决WindowsXP忘记密码/17-NewPassword.png "")  


### (3)重启电脑
拔出U盘，进入到登陆界面，登录即可


## (4)
不过要恢复成`一键ghost`之前的系统就很困难了😅(好在大部分数据还在，只不过最新的也只是2025.9月的)



## 4、踩坑记录
### (1)
我最开始试的F8进入高级选项
![](/assets/images/问题排查/操作系统与硬件/Windows/解决WindowsXP忘记密码/18-F8.png "")  
* `安全模式`: 也要密码
* `带命令行的安全模式`: 还是要密码(当时想着从命令行来改密码)


### (2)
然后插了U盘，电脑开机后按F8、F12、Esc、Delete都进不去那个`微PE系统`，发现Delete进入了某个设置(BIOS)后才设置好


### (3)
在`微PE系统`里面，我找了几次跟“密码修改”相关的工具，都没找到(可能是这个`微PE工具箱v1.3`有点老，或者是因为我制作启动盘的时候没有勾选`DOS工具箱`🤔)
![](/assets/images/问题排查/操作系统与硬件/Windows/解决WindowsXP忘记密码/19-Menu1.png "")  
![](/assets/images/问题排查/操作系统与硬件/Windows/解决WindowsXP忘记密码/20-Menu2.png "")  
![](/assets/images/问题排查/操作系统与硬件/Windows/解决WindowsXP忘记密码/21-Menu3.png "")  

然后文件资源管理器想搜索文件，发现竟然没有搜索功能😅翻了一圈才找到`文件快速搜索`(`Everything`)，然而也没有搜到AI说的`NTPWEdit`、`chntpw`这些密码修改工具


### (4)然后那个`Dism++`
* 工具箱里面没看到修改密码/账户相关的功能
![](/assets/images/问题排查/操作系统与硬件/Windows/解决WindowsXP忘记密码/22-Continue.png "")  
![](/assets/images/问题排查/操作系统与硬件/Windows/解决WindowsXP忘记密码/23-Dism++.png "")  

* 选择C盘 → `打开会话` → 它说`不支持此接口`😓
![](/assets/images/问题排查/操作系统与硬件/Windows/解决WindowsXP忘记密码/24-Click.png "")  

* 于是只能手动下载密码修改工具到U盘……



## 5、附
不喜欢偏硬件的东西，而且一想到我妈说写代码的还要会修电脑我就😡

不过靠自己和AI也是折腾出来了😄虽然说要想恢复原来的系统，可能得找专业人员了

---
