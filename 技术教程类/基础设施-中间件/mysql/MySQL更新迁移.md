# MySQL 更新迁移

---

---

## 背景

* win11, 本地开发用的 MySQL8.0.44，需要迁移到性能更好、支持更久的 8.4 版本
* 这里记录了迁移过程


## 备份数据并停止旧服务

* 创建备份文件夹，比如 `mysql-backup`
* cmd 执行数据导出（只导出业务数据库，不需要导出系统信息）

    ```shell
    :: 看看 mysqldump 所在路径, 引用这个绝对路径去导出数据
    where mysqldump
    :: 如果没有设置全局变量、没有输出，就去旧的 MySQL 文件夹目录，在 bin 文件夹里面找到 mysqldump.exe 复制绝对路径
    
    :: --databases：导出后面列出的库，替换为自己的数据库（mysql 命令行界面 `show databases;` 查看所有数据库）
    :: --single-transaction：InnoDB 一致性快照，导出期间不锁表
    :: --routines --triggers --events：带上存储过程、触发器、事件
    :: --set-gtid-purged=OFF：避免导入 8.4 时 GTID 状态冲突
    :: --default-character-set=utf8mb4：确保中文数据编码正确
    :: --result-file：由 mysqldump 直接写文件，避开 CMD 重定向的编码问题
    "D:\Users\Programming\Data-Base\MySql-Community\mysql-8.0.44-winx64\bin\mysqldump.exe" -u root -p --databases db01 dab02 dab03 --single-transaction --routines --triggers --events --set-gtid-purged=OFF --default-character-set=utf8mb4 --result-file="D:\Users\Programming\Data-Base\MySql-Community\mysql-backup\8.0.44_business_db.sql"
    ```
  
* 如果提示找不到 database, 可能是因为还没有把旧的 mysql 服务启动, 导致 mysqldump.exe 查不了数据

    ```shell
    :: 备注: 设计 sc 命令的操作在 cmd 进行, powersheel 没有 sc 命令
    :: 查找已有的 MySQL 服务名
    sc queryex type= service state= all | findstr /I "mysql"
    :: 我的输出
    :: SERVICE_NAME: MySQL
    :: DISPLAY_NAME: MySQL
    
    :: 启动旧的 MySQL 服务
    :: 把 mysql 替换为自己的服务名, 不区分大小写
    sc start mysql
    :: 查看 MySQL 服务状态
    sc query mysql
    ```

* 粗略扫一眼导出的数据...

    ```shell
    # 文件内搜索字符串 CREATE DATABASE 
    # 数量应该 = 实际的 dababase 数量
    
    # 文件末尾有完成导出的标记
    -- Dump completed on 2026-10-25 19:52:56
    ```
  
* 因为我这里只有 1 个 root 账号，所以没有管用户数据，如果有多个账号，可以导出账号相关的数据、grant 授权语句
* 管理员运行 cmd( `"C:\WINDOWS\system32\cmd.exe"` )停掉旧服务，避免干扰新版 MySQL 的初始化、后续配置

    ```shell
    :: 把 mysql 替换为自己的服务名, 不区分大小写
    sc stop mysql
    :: 状态应该是 STOPPED
    sc query mysql
    ```



## 下载新版本压缩包

* 来到[官网下载界面](https://downloads.mysql.com/archives/community/), 选择版本 8.4、操作系统 Windows，然后下载 zip 安装包

    ![01-download.png](/assets/images/技术教程类/基础设施-中间件/mysql/MySQL更新迁移/01-download.png)

* 解压，通常会有一个类似 `mysql-8.4.11-winx64` 的文件夹，里面装了基础配置
* 因为这个文件夹命名跟旧的 mysql 目录是区分开的，所以这里可以旧版、新版文件共存, 暂时不需要覆盖旧版本的文件
* 在新版本 mysql 文件夹根目录添加 `my.ini` 文件, 文件内容如下，用来指定端口、数据目录

    ```text
    [mysqld]
    # 设置 MySQL 的安装目录 (请替换为你的实际路径)
    basedir="D:/Users/Programming/Data-Base/MySql-Community/mysql-8.4.11-winx64"
    # 设置 MySQL 数据库的数据存放目录 (如果 data 文件夹不存在，可以手动创建)
    datadir="D:/Users/Programming/Data-Base/MySql-Community/mysql-8.4.11-winx64/data"
    # 设置你想要的端口，比如 3308
    port=3308
    # 允许最大连接数
    max_connections=200
    # 服务端使用的字符集
    character-set-server=utf8mb4
    # 默认存储引擎
    default-storage-engine=INNODB
    
    [mysql]
    # 设置 MySQL 客户端默认字符集
    default-character-set=utf8mb4
    
    [client]
    # 设置 MySQL 客户端连接服务端时默认使用的端口
    port=3308
    ```


## 大写的注意事项

* 提前说明，一定要用绝对路径
* 涉及 mysqld 的操作，使用 mysqld.exe 的绝对路径
* 不要简写为 mysqld, 避免用到旧版本的 mysqld.exe（导致注册出来的服务跑不动
* 切换命令行目录到新版本文件夹的 bin 再输入 mysqld 不一定管用，旧的全局变量可能会有干扰


## 初始化数据目录

* 管理员 cmd 进行初始化

    ```shell
    :: 切换到 8.4.11 的 bin 目录
    cd /d "D:\Users\Programming\Data-Base\MySql-Community\mysql-8.4.11-winx64\bin"
    
    :: 初始化数据目录
    :: --defaults-file 指定你的 my.ini 路径（确保配置生效）
    :: --initialize 生成 data 目录并创建临时 root 密码
    :: --console 将初始化日志输出到控制台，方便查看临时密码
    :: --upgrade=FORCE 强制按 8.4 规则构建数据字典
    :: --console 输出临时 root 密码到控制台，务必保存
    "D:\Users\Programming\Data-Base\MySql-Community\mysql-8.4.11-winx64\bin\mysqld.exe" --defaults-file="D:\Users\Programming\Data-Base\MySql-Community\mysql-8.4.11-winx64\my.ini" --initialize --console --upgrade=FORCE
    ```
  

## 注册并启动服务

* 管理员 cmd 注册新的 mysql 服务, 然后启动

    ```shell
    :: 切换到 8.4.11 的 bin 目录
    cd /d "D:\Users\Programming\Data-Base\MySql-Community\mysql-8.4.11-winx64\bin"
    
    :: 注册服务，并指定专属服务名 MySQL84
    :: 我这里把 --install 放在最前，后接 --defaults-file 指定 my.ini，如果 --defaults-file 放前面的话我这里会报错
    "D:\Users\Programming\Data-Base\MySql-Community\mysql-8.4.11-winx64\bin\mysqld.exe" --install MySQL84 --defaults-file="D:\Users\Programming\Data-Base\MySql-Community\mysql-8.4.11-winx64\my.ini"
    
    :: 看看服务状态( 服务名不区分大小写 )
    :: 注册失败的情况会输出服务未安装
    sc query mysql84
    
    :: 验证服务实际使用的程序路径
    sc qc mysql84
    ```

* `sc qc mysql84` 主要看它输出的 `BINARY_PATH_NAME` 路径有没有问题, 会显示注册服务用到的 mysqld、my.ini 绝对路径
  
* 注册失败的时候可以查看日志、复制粘贴给 AI

    ```shell
    :: 查看 8.4.11 数据目录下最新的 .err 日志尾部
    powershell -Command "Get-Content 'D:\Users\Programming\Data-Base\MySql-Community\mysql-8.4.11-winx64\data\*.err' -Tail 30"
    ```
  
* 启动服务

    ```shell
    :: 启动 8.4.11 服务
    :: sc start 或者 net start 都可以
    sc start mysql84
    
    :: 确认服务状态为 RUNNING
    sc query mysql84
    
    :: 确认 3308 端口( 自己的端口 )处于 LISTENING
    netstat -ano | findstr :3308
    ```


## 登录新的 mysql 并导入数据

* 登录

    ```shell
    :: 登录 8.4.11 实例
    :: -P 3308 指定端口，区分已经有的 mysql 服务端口
    mysql -u root -p
    :: 输入临时密码
    ```

* 修改密码

    ```mysql
    -- 将新密码替换为自己的强密码
    -- 8.4 默认认证插件为 caching_sha2_password，无需额外指定插件
    ALTER USER 'root'@'localhost' IDENTIFIED BY '你的新密码';
    ```

* 回到 cmd 导入数据

    ```shell
    :: 使用 8.4.11 的 mysql 客户端导入业务数据库
    :: -P 3308：显式连接新 8.4.11 实例
    :: < 将 SQL 文件内容作为输入喂给 mysql 客户端
    :: 导入过程中如遇单条语句报错会中断，需关注控制台输出
    "D:\Users\Programming\Data-Base\MySql-Community\mysql-8.4.11-winx64\bin\mysql.exe" -u root -p -P 3308 < "D:\Users\Programming\Data-Base\MySql-Community\mysql-backup\8.0.44_business_db.sql"
    ```

* 我遇到的报错情况是某一个物理外键引用的字段没有设置 unique，MySQL8.0 检查更宽松、MySQL8.4 检查更严，就报错了
* 临时的解决操作是复制一份数据 sql 文件，去掉前面已经导入的 database 和 table, 临时关闭外键检查，从中断的地方开始，`use 对应的 database`, 再来导入剩下的数据, 之后查看有问题的表结构、调整结构

    ```shell
    :: 在 sql 文件里面加上开关
    -- 临时关闭严格外键检查（仅对当前会话生效，重启后失效）
    SET SESSION restrict_fk_on_non_standard_key = OFF;
    
    :: 然后导入剩下的数据
    mysql -u root -p -P 3308 < "D:\Users\Programming\Data-Base\MySql-Community\mysql-backup\8.0.44_business_db_remaining.sql"
    ```

* 用 IDE 连接数据源，切换到新的 MySQL 端口，可以进行简单的数据查询、修改


## 修改环境变量

* 修改系统变量
* 设置 → 系统 → 最下面 `关于` → 找到 `高级系统设置` → `环境变量`,
* 把 `MYSQL_HOME` 变量的值改为新版本 MySQL 所在的文件夹绝对路径, 
* 如果没有这个变量可以新增一个, 然后 Path 里面设置 `%MYSQL_HOME%\bin`
* 一路确定
* 新开 cmd 检查环境

    ```shell
    mysql --version
    where mysql
    where mysqld
    ...
    ```
  
* 我这里发现 `where xxx` 出现了新版本、旧版本的路径，发现是用户变量还有一个旧版本 mysql 的绝对路径
* 于是直接删除用户变量里面的旧 mysql 路径，只留新版本

    ```shell
    # 将当前的用户 Path 变量值保存到 D 盘，便于出错时还原
    [System.Environment]::GetEnvironmentVariable('Path', 'User') | Out-File "D:\UserPath_Backup.txt"
    
    # 1. 获取当前用户的 Path 变量（持久化存储中的值，非当前会话）
    $userPath = [System.Environment]::GetEnvironmentVariable('Path', 'User')
    
    # 2. 按分号拆分成数组，过滤掉目标路径（-ne 表示“不等于”，不区分大小写可选 -ne 或 -cne）
    # 这里改成自己要删除的路径
    $targetToRemove = 'D:\Users\Programming\Data-Base\MySql-Community\mysql-8.0.44-winx64\bin'
    $newPathArray = $userPath.Split(';') | Where-Object { $_ -ne $targetToRemove -and $_.Trim() -ne '' }
    
    # 3. 重新用分号连接成字符串
    $newPath = $newPathArray -join ';'
    
    # 4. 写回用户环境变量（第二个参数 $null 位置传值，第三个参数 'User' 指定作用域为当前用户）
    [System.Environment]::SetEnvironmentVariable('Path', $newPath, 'User')
    
    # 5. 输出结果供确认
    Write-Host "已移除路径。新的用户 Path 为：" -ForegroundColor Green
    Write-Host $newPath
    ```


## 旧版本处理

* 目前没删除旧服务，作为回滚，过一段时间感觉没问题就可以删了
* `mysql -uroot -p` 默认进入现在设置的新版本 MySQL（启动服务的前提下）
* 要进入旧服务，就需要显式指定旧的端口 `mysql -uroot -p -P3307`
* 进去后用 `show variables like 'port';` 查看当前的端口号确认是新的还是旧的
* 删除旧版本的操作
* 删服务( 管理员 cmd )
    
    ```shell
    :: 停止服务
    sc stop mysql
    :: 看看状态
    sc query mysql
    :: 删除
    sc delete mysql
    :: 应该是没有了
    sc query mysql
    ```
  
* 删旧的文件夹, 包括 data 数据目录、my.ini 配置
* 清理注册表




