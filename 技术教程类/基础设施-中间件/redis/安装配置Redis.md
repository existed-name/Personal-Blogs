# 安装配置Redis

---

---

* 笔者的配置: win11 + wsl2 + 最新的 redis

## 安装 wsl

* redis 原生不支持 windows, 需要装在 Linux 上, 所以要装在 Windows 通常选择装进 WSL 或者 Docker 容器
* 这里选择用 wsl 安装 redis, 详见<u>[安装WSL](/技术教程类/虚拟机/wsl/安装WSL.md)</u>

## 安装 redis

1. cmd 或者 PowerShell 命令行 `wsl -d ubuntu` 进入 wsl
2. 输入 ` sudo apt update && sudo apt install redis-server -y` 更新软件包并安装 redis 服务
3. 启动 redis 服务 `sudo service redis-server start`, 默认端口 6379
4. `sudo service redis-server status` 查看是否在运行
5. 也可以输入 ` redis-cli ping` 如果输出 `PONG` 则说明 redis 服务正在运行
6. 运行服务后输入 `redis-cli` 可以进入 redis 的命令行，然后拿着网络资料/ai 给的命令就可以简单测试
7. 设置密码
    * 如果进入了 `redis-cli` 就输入 `exit` 退出
    * 进行以下操作

    ```shell
    # 1. 备份原配置
    sudo cp /etc/redis/redis.conf /etc/redis/redis.conf.bak
    
    # 2. 编辑配置文件
    sudo nano /etc/redis/redis.conf
    ```

   * 在 nano 界面按 ctrl + f 输入 `requirepass` 并回车，来到密码设置的位置

       ![01-set-redis-password.png](/assets/images/技术教程类/基础设施-中间件/redis/安装配置Redis/01-set-redis-password.png)

       ```shell
       # 3. 找到 `requirepass foobared` 这一行
       # 在后面新加一行
       requirepass 自己的密码
    
       # 4. Ctrl+O 再回车保存，然后 Ctrl+X 退出
    
       # 5. 重启 Redis 使配置生效
       sudo service redis-server restart
       ```

8. 验证密码
    * `redis-cli ping` 会报错需要认证
    * `redis-cli -a 密码 ping` 可以输出 PONG

    ![02.png](/assets/images/技术教程类/基础设施-中间件/redis/安装配置Redis/02.png)

## 设置项目配置文件

* 对于 SpringBoot 项目，假如配置文件有 `application.yaml`( 放占位的模板、不怕泄露的配置 ) 和 `application-dev.yaml`( 开发用的配置，被 git 排除 ) 
* 给 `application.yaml` 补充配置

    ```yaml
    spring:
      redis:
        # 这里的 project 替换为自己的项目名
        host: ${project.redis.host}
        port: ${project.redis.port}
        password: ${project.redis.password}
        database: ${project.redis.database}
    ```

* 给 `application-dev.yaml` 补充配置

    ```yaml
    # 这里的 project 替换为自己的项目名
    project:
      redis:
        # 主机 IP, localhost = 127.0.0.1
        host: localhost
        # 端口, 默认 6379
        port: 6379
        password: # 自己的密码, 如果没设置密码可以留空
        # 推荐用 0 号数据库
        database: 0
    ```
  
* 然后配置 redisTemplate 就可以在项目里面进行缓存读写了

## 安装 RedisInsight

* 数据库通常都有对应的可视化工具，MySQL 可以在 IDEA/DataGrip 可视化编辑，而 Redis 的可视化工具则有 RedisInsight、Another-Redis-Desktop-Manager 等等
* 这里选择安装官方推荐的 RedisInsight
* <u>[RedisInsight 官方下载界面](https://github.com/redis/RedisInsight/releases)</u>
* 下载 `Redis-Insight-win-installer.exe`

    ![03-redis-insight.png](/assets/images/技术教程类/基础设施-中间件/redis/安装配置Redis/03-redis-insight.png)

* 一步一步安装即可

    ![04-start-install.png](/assets/images/技术教程类/基础设施-中间件/redis/安装配置Redis/04-start-install.png)
    ![05.png](/assets/images/技术教程类/基础设施-中间件/redis/安装配置Redis/05.png)
    ![06.png](/assets/images/技术教程类/基础设施-中间件/redis/安装配置Redis/06.png)
    ![07-redis-insight-logo.png](/assets/images/技术教程类/基础设施-中间件/redis/安装配置Redis/07-redis-insight-logo.png)

* 隐私设置

    ![08-privacy-settings.png](/assets/images/技术教程类/基础设施-中间件/redis/安装配置Redis/08-privacy-settings.png)
    ![09.png](/assets/images/技术教程类/基础设施-中间件/redis/安装配置Redis/09.png)

* 然后就可以看到通过 redisTemplate 写入的缓存数据了( redisTemplate 序列化器问问 ai 怎么配, 避免乱码 )

    ![10.png](/assets/images/技术教程类/基础设施-中间件/redis/安装配置Redis/10.png)
    ![11.png](/assets/images/技术教程类/基础设施-中间件/redis/安装配置Redis/11.png)




