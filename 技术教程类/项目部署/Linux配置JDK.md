# Linux配置JDK

**目录**

- [1、配置清单](#1配置清单)
- [2、下载JDK](#2下载jdk)
- [3、上传JDK](#3上传jdk)
- [4、配置JDK](#4配置jdk)

---

---

## 1、配置清单
* VirtualBox7.2(如果想方便复制粘贴命令，可参考文章<u>[虚拟机和主机共享剪贴板](https://zhuanlan.zhihu.com/p/1979949196330176615)</u>配置增强功能)
* Debian13
* FinalShell4.6(上传文件)
* JDK21
* 系列文章: <u>[Linux部署SpringBoot项目](/技术教程类/项目部署/Linux部署SpringBoot项目.md)</u>



## 2、下载JDK
1. 来到Oracle官网下载界面: <u>https://www.oracle.com/java/technologies/downloads/#jdk21-linux</u>

   ![](/assets/images/技术教程类/项目部署/Linux配置JDK/01-DownloadJdk.png "")

2. 选择JDK版本 → 选择操作系统(Linux) → 选择Linux发行版通用的`x64 Compressed Archive`压缩包(`jdk-21_linux-x64_bin.tar.gz`)


## 3、上传JDK
* 打开FinalShell，连接虚拟机(可参考文章:<u>[FinalShell连接虚拟机](/技术教程类/项目部署/FinalShell连接虚拟机.md)</u>)，切换到目标目录后，上传压缩包

* 比如我放在`/home/student/Programming/Java-Develop/JDK/jdk-21_linux-x64_bin.tar.gz`


## 4、配置JDK
1. 创建安装目录并解压

    ```bash
   # 普通用户的每条命令加上sudo防止命令权限不够
    sudo mkdir -p /opt/jdk-21
   # 解压到opt文件夹，通常放用户自己安装的东西
    sudo tar -xzf /home/student/Programming/Java-Develop/JDK/jdk-21_linux-x64_bin.tar.gz -C /opt/jdk-21 --strip-components=1
   # --strip-components=1 去掉最外层文件夹，直接把内容放到 /opt/jdk-21 下
    ```

2. 验证解压是否成功

    ```bash
   # 应该可以看到java, javac, jar等等文件
   ls /opt/jdk-21/bin
   ```

3. 配置系统级环境变量

   ```bash
   # 使用 tee 写入环境变量文件（无需交互式编辑器）
   sudo tee /etc/profile.d/jdk21.sh << 'EOF'
   export JAVA_HOME=/opt/jdk-21
   export PATH=$JAVA_HOME/bin:$PATH
   EOF
   ```
   
4. 立即生效 + 永久生效

   ```shell
   source /etc/profile.d/jdk21.sh
   ```

5. (可跳过)使用 Debian alternatives 系统管理 java 命令（方便以后多版本切换）

   ```shell
   update-alternatives --install /usr/bin/java java /opt/jdk-21/bin/java 100
   update-alternatives --install /usr/bin/javac javac /opt/jdk-21/bin/javac 100
   ```

6. 验证安装

   ```shell
   java -version
   javac -version
   echo $JAVA_HOME
   
   # 预期输出
   java version "21.x.x" ...
   javac 21.x.x
   /opt/jdk-21
   ```

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
