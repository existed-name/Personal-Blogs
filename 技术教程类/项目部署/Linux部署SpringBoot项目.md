# Linux部署SpringBoot项目

## 目录
- [1、配置清单](#1配置清单)
- [2、配置虚拟机](#2配置虚拟机)
  - [(1) 下载安装](#1下载安装)
  - [(2) 配置SSH](#2配置ssh)
- [3、配置软件包](#3配置软件包)
  - [(1) 配置JDK](#1配置jdk)
  - [(2) 配置MySQL+数据迁移](#2配置mysql数据迁移)
  - [(3) SpringBoot项目打包](#3springboot项目打包)
  - [(4) Vue项目打包+配置Nginx](#4vue项目打包配置nginx)

---

---

# 1、配置清单
> 等下会有下载配置的教程

1. 虚拟机软件: VirtualBox7.2.4

2. 虚拟机系统: Debian13.1.0

3. SSH客户端软件: FinalShell4.6.5(上传文件用)

4. 项目运行软件包
    * JDK21
    * MySQL8.0
    * Nginx1.28
    * SpringBoot项目打成的FatJar包
    * Vue项目build的dist包

5. 注: 
   * 虚拟机软件、系统都是上学期实验课弄好的，就偷懒没去装VMware，以后试下用VMware部署项目……
   * 这里为了不太长，拆成了几篇文章把链接放在文中，跳来跳去可能不方便、感谢谅解(解耦，结果更加耦合了🤣)



# 2、配置虚拟机
## (1)下载安装
下载安装可参考往期文章: 
  1. <u>[安装VirtualBox7.2.4](https://zhuanlan.zhihu.com/p/1968673825353863573)</u>
  2. <u>[解决VirtualBox安装目录无效的问题](https://zhuanlan.zhihu.com/p/1968693323565863053)</u>
  3. <u>[VirtualBox创建虚拟机](https://zhuanlan.zhihu.com/p/1968948685741225905)</u>

并且建议给VirtualBox配上增强功能，方便虚拟机跟主机之间互相复制粘贴(默认不能互相复制，手敲就比较麻烦)
* <u>[虚拟机和主机共享剪贴板](https://zhuanlan.zhihu.com/p/1979949196330176615)</u>


## (2)配置SSH
可参考这篇文章: <u>[FinalShell连接虚拟机](/技术教程类/项目部署/FinalShell连接虚拟机.md)</u>



# 3、配置软件包
* 这里按照后端 → 前端的顺序进行，JDK → MySQL + 数据迁移 → FatJar → dist → Nginx
* 部分命令，比如项目名称/文件夹，记得改成自己的(可以让AI改)

## (1)配置JDK
可参考这篇文章: <u>[Linux配置JDK](/技术教程类/项目部署/Linux配置JDK.md)</u>


## (2)配置MySQL+数据迁移
可参考这篇文章: <u>[Linux配置MySQL+数据迁移](/技术教程类/项目部署/Linux配置MySQL-数据迁移.md)</u>


## (3)SpringBoot项目打包
* **确保application.yaml的数据库username、password = 虚拟机数据库**

1. 确保 pom.xml 中有 `spring-boot-maven-plugin`

   ```xml
        <build>
            <!--   指定jar包名称(添加 backend 用来区分前后端) -->
            <finalName>tlias-management-backend</finalName>
   
           <plugins>
               <plugin>
                   <groupId>org.springframework.boot</groupId>
                   <artifactId>spring-boot-maven-plugin</artifactId>
                   <configuration>
                       <excludes>
                           <exclude>
                               <groupId>org.projectlombok</groupId>
                               <artifactId>lombok</artifactId>
                           </exclude>
                       </excludes>
                   </configuration>
               </plugin>
           </plugins>
       </build>
   ```

2. IDEA右侧Maven 面板 → 当前项目的Lifecycle → 点击clean → 再点击package
   * 或者当前项目目录，终端运行 `mvn clean package`

   ![](/assets/images/技术教程类/项目部署/Linux部署SpringBoot项目/01-MavenCleanPackage.png "")

3. 找到项目的 `target`文件夹，里面有`项目名称-版本号-SNAPSHOT.jar`,就是包含依赖、可在JRE下运行的FatJar包
   * 另外一个`.jar.origin`文件不包含依赖，不管他
   * 在命令行执行`java -jar xxx.jar`运行jar包，先测试有没有问题，再放到虚拟机上面

4. 虚拟机创建文件夹

   ```shell
   # 1. 创建目录结构
   # 后续可以用 /opt/apps/项目名称 区分不同项目
   sudo mkdir -p /opt/apps/tlias-management/logs # 记得换成自己的项目名
   # 方便操作文件
   sudo chown -R student:student /opt/apps/tlias-management # student换成自己的虚拟机用户
   ```

5. 上传这个Jar包到文件夹，`/opt/apps/tlias-management/tlias-management-backend.jar`

6. 创建后端 systemd 服务（开机自启）

   ```shell
   # 1. 创建日志目录并授权
   sudo mkdir -p /opt/apps/tlias-management/logs
   sudo chown -R student:student /opt/apps/tlias-management
   
   # 2. 创建服务文件
   sudo tee /etc/systemd/system/tlias.service > /dev/null <<EOF
   [Unit]
   Description=Tlias Management Backend (Spring Boot)
   After=network.target mysql.service
   Wants=mysql.service
   
   [Service]
   Type=simple
   User=student
   WorkingDirectory=/opt/apps/tlias-management
   ExecStart=/opt/jdk-21/bin/java -jar tlias-management-backend.jar --server.port=8081
   SuccessExitStatus=143
   Restart=always
   RestartSec=5
   StandardOutput=append:/opt/apps/tlias-management/logs/tlias.log
   StandardError=append:/opt/apps/tlias-management/logs/tlias-error.log
   
   [Install]
   WantedBy=multi-user.target
   EOF
   
   
   # 3. 重新加载并启动
   sudo systemctl daemon-reload
   sudo systemctl enable tlias
   sudo systemctl start tlias
   sudo systemctl status tlias # 看是否 Active: active (running)
   ```


## (4)Vue项目打包+配置Nginx
可参考这篇文章: <u>[Linux配置Nginx-Vue打包](/技术教程类/项目部署/Linux配置Nginx-Vue打包.md)</u>，到这里就成功部署项目了🎉

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
