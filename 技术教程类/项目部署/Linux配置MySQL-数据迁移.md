# Linux配置MySQL+数据迁移

**目录**

- [1、配置清单](#1配置清单)
- [2、下载MySQL压缩包](#2下载mysql压缩包)
- [3、上传压缩包](#3上传压缩包)
- [4、配置MySQL](#4配置mysql)
    - [(1) 检查是否已有数据库](#1-检查是否已有数据库)
    - [(2) 手动安装 MySQL](#2-手动安装-mysql)
    - [(3) 权限设置](#3-权限设置)
    - [(4) 初始化数据库](#4-初始化数据库)
    - [(5) 依赖问题修复](#5-依赖问题修复)
    - [(6) 配置环境变量](#6-配置环境变量)
    - [(7) 配置systemd服务](#7-配置systemd服务开机自启)
    - [(8) 登录 & 修改 root 密码](#8-登录--修改-root-密码)
    - [(9) 重新登录+查看数据库列表验证成功](#9-重新登录查看数据库列表验证成功)
- [5、数据迁移](#5数据迁移)
  - [(1) 导出数据方法1：命令行操作](#1导出数据方法1命令行操作)
  - [(2) 导出数据方法2：图形化界面](#2导出数据方法2图形化界面)
  - [(3) 导入数据](#3导入数据)

---

---

# 1、配置清单
* VirtualBox7.2(如果想方便复制粘贴命令，可参考文章<u>[虚拟机和主机共享剪贴板](https://zhuanlan.zhihu.com/p/1979949196330176615)</u>配置增强功能)
* Debian13
* FinalShell4.6(上传文件)
* MySQL8.0(不过好像今年4月份就EOL了，可以换成LTS的8.4版本)
* 系列文章: <u>[Linux部署SpringBoot项目](/技术教程类/项目部署/Linux部署SpringBoot项目.md)</u>



# 2、下载MySQL压缩包
1. 来到官网下载界面: <u>https://downloads.mysql.com/archives/community/</u>
   
   ![](/assets/images/技术教程类/项目部署/Linux配置MySQL-数据迁移/01-DownloadMysql.png "")

2. 筛选

    * 版本(比如8.4.x)
    * 操作系统(`Linux - Generic`)
    * 操作系统版本(`x86-64位`,然后`glibc`是Linux基础库版本，系统的`glibc`版本必须 >= 这里包要求的版本，Debian13直接上数字大的那个包版本就行)

3. 下载`Compressed TAR Archive`


# 3、上传压缩包
* 打开FinalShell，连接虚拟机(可参考文章:<u>[FinalShell连接虚拟机](/技术教程类/项目部署/FinalShell连接虚拟机.md)</u>)，切换到目标目录后，上传压缩包(或者拖拽上传)

* 比如我放在`/home/student/Programming/Data-Base/MySQL-Community/mysql-8.0.44-linux-glibc2.28-x86_64.tar.xz`


# 4、配置MySQL

> 这里是多次试错、弄好过后把命令行全部复制下来，让AI再整理的步骤

## (1) 检查是否已有数据库

```shell
# 列出已安装的软件包，筛选mysql/mariadb
dpkg -l | grep -E 'mysql|mariadb'

# 如果上面有输出，进行以下清理操作
# 更新apt软件源索引
sudo apt update

# 停止可能存在的mysql/mariadb服务(忽略不存在报错)
sudo systemctl stop mysql mariadb 2>/dev/null || true

# 卸载mysql/mariadb相关包(包含配置)
sudo apt purge -y mysql* mariadb*

# 清理无用依赖
sudo apt autoremove -y

# 删除残留配置/数据目录
sudo rm -rf /etc/mysql /var/lib/mysql /var/log/mysql
```

## (2) 手动安装 MySQL

```shell
# 创建安装目录
sudo mkdir -p /opt/mysql-8.0

# 解压MySQL二进制包到指定目录(去掉最外层目录)
sudo tar -xJf MySQL压缩包路径 -C /opt/mysql-8.0 --strip-components=1

# 创建mysql用户组
sudo groupadd mysql

# 创建mysql系统用户(禁止登录)
sudo useradd -r -g mysql -s /bin/false mysql

# 创建数据目录(存放数据库文件)
sudo mkdir -p /opt/mysql-8.0/data
```

## (3) 权限设置

```shell
# 程序目录归root(防止被篡改)
sudo chown -R root:root /opt/mysql-8.0

# 设置目录权限(可读可执行)
sudo chmod -R 755 /opt/mysql-8.0

# 数据目录归mysql用户(mysqld进程需要写权限)
sudo chown -R mysql:mysql /opt/mysql-8.0/data

# 数据目录限制权限(更安全)
sudo chmod -R 750 /opt/mysql-8.0/data
```

## (4) 初始化数据库

```shell
# 初始化数据库(生成系统表+root临时密码)
/opt/mysql-8.0/bin/mysqld --initialize \
--user=mysql \
--basedir=/opt/mysql-8.0 \
--datadir=/opt/mysql-8.0/data

# 输出中会生成 root 临时密码(必须记录)
```

## (5) 依赖问题修复

* 如果初始化数据库报错`error while loading shared libraries: libaio.so.1: cannot open shared object file: No such file or directory`,也就是`libaio.so.1 not found`

* 执行以下命令，然后重新初始化数据库(第4步)

  ```shell
  # 更新软件源
  sudo apt update

  # 安装libaio(MySQL依赖的异步IO库)
  sudo apt install libaio1t64

  # 建立软链接(兼容MySQL旧版本依赖名 libaio.so.1)
  sudo ln -s /usr/lib/x86_64-linux-gnu/libaio.so.1t64 \
  /usr/lib/x86_64-linux-gnu/libaio.so.1
  ```

## (6) 配置环境变量

```shell
# 写入全局环境变量文件
sudo tee /etc/profile.d/mysql.sh << 'EOF'
export MYSQL_HOME=/opt/mysql-8.0
export PATH=$MYSQL_HOME/bin:$PATH
EOF

# 立即加载环境变量
source /etc/profile.d/mysql.sh

# 验证
which mysql # 预期输出 /opt/mysql-8.0/bin/mysql
mysql --version
```

## (7) 配置systemd服务(开机自启)

* 注意: 这里不能用`cat > `或者`sudo cat > `(从键盘读取内容、用` > `重定向写入文件)，
* 可能没权限或者重启后失效，应该用`sudo tee`，

```shell
# 写入systemd服务文件(用tee避免权限问题)
sudo tee /etc/systemd/system/mysql.service << 'EOF'
[Unit]
Description=MySQL Server
After=network.target

[Service]
Type=simple
User=mysql
Group=mysql

# 启动mysqld进程
ExecStart=/opt/mysql-8.0/bin/mysqld \
--basedir=/opt/mysql-8.0 \
--datadir=/opt/mysql-8.0/data \
--log-error=/opt/mysql-8.0/data/error.log

# 失败自动重启
Restart=on-failure
RestartSec=5

# 提高文件描述符上限
LimitNOFILE=65535

# 指定pid文件
PIDFile=/opt/mysql-8.0/data/mysqld.pid

[Install]
WantedBy=multi-user.target
EOF

# 重新加载systemd配置
sudo systemctl daemon-reload

# 设置开机自启
sudo systemctl enable mysql

# 启动mysql服务
sudo systemctl start mysql

# 查看服务状态
sudo systemctl status mysql
```

## (8) 登录 & 修改 root 密码

```shell
# 使用临时密码登录
mysql -u root -p

-- 修改root密码(必须执行，否则权限受限)
ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';

-- 刷新权限表
FLUSH PRIVILEGES;

-- 退出
EXIT;
```

## (9) 重新登录+查看数据库列表验证成功

```shell
mysql -u root -p

SHOW DATABASES;
```


# 5、数据迁移
## (1)导出数据方法1:命令行操作

1. 命令行或者IDEA的Terminal切换到用来放`.sql`文件的目录(比如新建`db`/`database`文件夹)

2. 执行`mysqldump -u MySQL用户名 -p --databases 目标Schema名称 > 名称.sql`
   * MySQL用户名用自己的或者root

3. 然后就可以导出带有`CREATE DATABASE`的`.sql`文件

4. 有个巨大的坑是IDEA的Terminal默认PowerShell，导出来是`UTF-16LE`、中文乱码
   * Setting → Tool → Terminal → Shell Path修改成cmd.exe就好了

   ![](/assets/images/技术教程类/项目部署/Linux配置MySQL-数据迁移/02.png "")

  * 甚至再保守起见，还可以手动指定编码
   
  ```shell
  mysqldump ^
  -u root -p ^
  --default-character-set=utf8mb4 ^
  --databases xxx ^
  --result-file=xxx.sql
  ```


## (2)导出数据方法2:图形化界面
1. 回到IDEA，打开右侧`Database`面板

2. 右键目标Schema → 下面`Import/Export` → `Export with 'mysqldump' ...`

   ![](/assets/images/技术教程类/项目部署/Linux配置MySQL-数据迁移/03-ExportSql.png "")

3. 选择导出目录、Options用默认勾选就可以了

   ![](/assets/images/技术教程类/项目部署/Linux配置MySQL-数据迁移/04.png "")

4. 注意**导出的`.sql`需要手动补充`create database`语句**

   ```shell
   -- 手动在文件最开头添加(在所有 SET 语句之前)
   CREATE DATABASE IF NOT EXISTS tlias
       CHARACTER SET utf8mb4
       COLLATE utf8mb4_unicode_ci;
   
   USE tlias;
   ```

   ![](/assets/images/技术教程类/项目部署/Linux配置MySQL-数据迁移/05.png "")


## (3)导入数据
* 然后登录虚拟机的MySQL,执行`source sql文件绝对路径`

* 如果没有在`.sql`文件补充创建数据库语句，可以手动在虚拟机创建数据库，然后导入

   ```shell
   CREATE DATABASE IF NOT EXISTS 数据库名 CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
   EXIT;
   
   # 导入到指定数据库
   mysql -u root -p 数据库名 < sql文件绝对路径
   ```

* 然后`show databases`、`use xxx`、`show tables`检查数据

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
