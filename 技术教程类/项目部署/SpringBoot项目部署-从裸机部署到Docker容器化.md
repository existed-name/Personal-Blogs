# SpringBoot项目部署:从裸机部署到Docker容器化

**目录**

- [1、配置清单](#1配置清单)
- [2、Docker安装配置](#2docker安装配置)
  - [(1)安装依赖](#1安装依赖)
  - [(2)配置镜像加速](#2配置镜像加速)
  - [(3)验证安装](#3验证安装)
  - [(4)非root用户权限配置](#4非-root-用户权限配置)
- [3、配置MySQL容器](#3配置mysql容器)
  - [(1)停止当前MySQL服务](#1停止当前-mysql-服务)
  - [(2)创建Docker专用数据卷目录](#2创建-docker-专用数据卷目录)
  - [(3)启动MySQL容器](#3启动-mysql-容器)
  - [(4)配置宿主机连接权限](#4配置宿主机连接权限)
- [4、配置后端容器](#4配置后端容器)
  - [(1)停止原来的后端服务](#1停止原来的后端服务)
  - [(2)创建标准目录结构](#2创建标准目录结构)
  - [(3)准备jar包](#3准备-jar-包)
  - [(4)写入Dockerfile](#4写入-dockerfile)
  - [(5)创建网络](#5创建网络)
  - [(6)构建镜像+启动容器](#6构建镜像--启动容器)
  - [(7)验证网络+容器](#7验证网络--容器)
- [5、配置前端容器](#5配置前端容器)
  - [(1)停止原服务](#1停止原服务)
  - [(2)创建前端目录结构](#2创建前端目录结构)
  - [(3)创建Nginx配置文件](#3创建-nginx-配置文件)
  - [(4)创建前端Dockerfile](#4创建前端-dockerfile)
  - [(5)构建镜像+启动容器](#5构建镜像--启动容器)
  - [(6)验证状态](#6验证状态)
  - [(7)跑不通的处理方法](#7跑不通的处理方法)
- [6、DockerCompose编排](#6dockercompose-编排)
  - [(1)处理旧容器](#1处理旧容器)
  - [(2)修改后端](#2修改后端)
  - [(3)创建docker-compose.yml](#3创建-docker-composeyml)
  - [(4)Compose启动所有服务](#4compose-启动所有服务)

---

---

> 记录裸机部署 SpringBoot 项目后，把各个设施(MySQL,Nginx,Jar,Dist)迁移到 Docker 容器的过程

# 1、配置清单
1. 虚拟机软件: VirtualBox7.2.4

2. 虚拟机系统: Debian13.1.0

3. SSH客户端软件: FinalShell4.6.5(上传文件用,相关教程可见<u>[FinalShell连接虚拟机](/技术教程类/项目部署/FinalShell连接虚拟机.md)</u>)

4. 项目运行软件包
    * JDK21
    * MySQL8.0
    * Nginx1.28
    * SpringBoot项目打成的FatJar包
    * Vue项目build的dist包

5. 注: 
    * 上期文章<u>[Linux部署SpringBoot项目](/技术教程类/项目部署/Linux部署SpringBoot项目.md)</u>是直接把Jar包、基础设施(MySQL/Nginx)放到虚拟机里面跑(`裸机部署`), 而这期文章记录的是**把裸机部署的项目迁移到Docker容器**进行管理(`容器化`)
    * 文中的命令是自己跑通AI的教程后整理的，再让AI审核了一遍
    * 如果读者遇到问题，可以直接复制Terminal的情况问AI(记得给出配置、版本号)
    * 并且建议给VirtualBox配上增强功能，方便虚拟机跟主机之间互相复制粘贴(默认不能互相复制，手敲就比较麻烦): <u>[虚拟机和主机共享剪贴板](https://zhuanlan.zhihu.com/p/1979949196330176615)</u>



# 2、Docker安装配置
## (1)安装依赖
```shell
# 1. 更新系统并安装依赖
sudo apt update && sudo apt upgrade -y
sudo apt install ca-certificates curl gnupg lsb-release -y

# 2. 创建 keyrings 目录（官方推荐方式）
sudo install -m 0755 -d /etc/apt/keyrings

# 3. 添加 Docker 官方 GPG 密钥
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# 4. 添加 Docker 官方 apt 源（自动适配 Debian 13 trixie）
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 5. 更新 apt 并安装 Docker Engine + Compose
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y

# 6. 启动并设置开机自启动
sudo systemctl start docker
sudo systemctl enable docker
```


## (2)配置镜像加速
防止网络访问 Docker Hub 被拒

```shell
# 1. 创建配置目录
sudo mkdir -p /etc/docker

# 2. 写入加速器（推荐国内源）
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": [
    "https://docker.m.daocloud.io",
    "https://mirror.ccs.tencentyun.com"
  ]
}
EOF

# 3. 重启 Docker
sudo systemctl daemon-reexec
sudo systemctl restart docker
```


## (3)验证安装
```shell
# 验证安装（看到 Hello from Docker 即成功）
sudo docker run hello-world
```

* 注: 
* 虚拟机直接`ping github.com`，如果通了，那么整个虚拟机的网络都是可以的(甚至不用单独配Docker的镜像加速)
* 如果`ping`不通、Docker拉取`hello-world`失败，直接关掉虚拟机，**从VirtualBox的虚拟机界面重新启动虚拟机**(跟命令`reboot`重启的效果不一样)


## (4)非 root 用户权限配置
这样可以无 sudo 运行 Docker

```shell
# 创建 docker 组（如果不存在）
sudo groupadd docker

# 把当前用户加入 docker 组
sudo usermod -aG docker $USER

# 立即生效（新组权限）
newgrp docker

# 测试非 root 运行 docker
docker run hello-world
```



# 3、配置MySQL容器
## (1)停止当前 MySQL 服务
（避免端口冲突）

```shell
# 停止并禁用当前系统 MySQL 服务（Docker 化后不再需要它自启动）
sudo systemctl stop tlias-mysql   # 如果你的服务名不是这个，请替换为实际服务名
sudo systemctl disable tlias-mysql

# 确认已停止
sudo systemctl status mysql*   # 看到 inactive/dead 即可

# 应该没有输出
systemctl list-units --type=service | grep mysql
```


## (2)创建 Docker 专用数据卷目录

1. 这里是目录结构

   ```text
   /data/docker/mysql/
   ├── data/     ← 存放数据库文件（已复制你的旧数据）
   ├── logs/     ← 日志（可选）
   └── conf/     ← 后面可放自定义 my.cnf
   ```

2. 创建目录、设置权限

   ```shell
   # 建立标准数据目录
   sudo mkdir -p /data/docker/mysql/{data,logs,conf}
   
   # 确认镜像内 mysql 用户 UID(通常是999)
   docker run --rm mysql:8.0 id mysql # 把8.0换成自己的版本号，比如8.4
   # 输出类似 uid=999(mysql) gid=999(mysql) groups=999(mysql)
   # 可能会先去拉取 MySQL 镜像
   
   # 设置权限
   # sudo chown -R UID:GID 目标目录
   sudo chown -R 999:999 /data/docker/mysql/data # 注意自己的是不是999
   sudo chown -R 999:999 /data/docker/mysql/logs
   sudo chown -R 999:999 /data/docker/mysql/conf 
   
   # 权限设置（目录可读写）
   sudo chmod -R 750 /data/docker/mysql/data
   sudo chmod -R 750 /data/docker/mysql/logs
   sudo chmod -R 750 /data/docker/mysql/conf
   ```

3. 复制数据
   * 当时脑子抽了，没看到这一步，直接启动容器，发现没有自己项目的 schema
   * 然后停止、删除容器，重新来过，折腾一段时间才弄好

   ```shell
   # 复制数据（-a 保留权限和属性）
   # 左边是裸机部署 MySQL 的 data 目录，右边是容器里面的数据目录
   sudo cp -a /opt/mysql-8.0/data/. /data/docker/mysql/data/
   
   # 再次修正权限（复制后可能变）
   sudo chown -R 999:999 /data/docker/mysql/data
   ```


## (3)启动 MySQL 容器

* 注意: 
* 如果要定制自己的镜像(把自己的打包软件塞进去)，就需要Dockerfile + 构建镜像 
* 直接用官方镜像，不需要构建
* 所以这里的 MySQL 容器没有构建步骤
* 记得把这里面出现的 `tlias` 换成自己的项目名

1. 启动命令
   * 这个是不带注释的

   ```shell
   docker run -d \
   --name tlias-mysql \
   -p 3306:3306 \
   -e MYSQL_ROOT_PASSWORD=123456 \
   -v /data/docker/mysql/data:/var/lib/mysql \
   -v /data/docker/mysql/logs:/var/log/mysql \
   -v /data/docker/mysql/conf:/etc/mysql/conf.d \
   --restart unless-stopped \
   mysql:8.0
   ```

   * 这个是带注释的
   * 注意反斜杠 `\` 后面不能有任何字符（包括空格和注释），所以只是看看命令的作用，不能放到Terminal里面

   ```shell
   docker run -d \
   # 容器名（自定义）
     --name tlias-mysql \
   # 端口映射
     -p 3306:3306 \
   # 迁移数据之前的 MySQL 服务的密码
     -e MYSQL_ROOT_PASSWORD=123456 \
   # 数据目录(虚拟机目录 → 容器内目录)
     -v /data/docker/mysql/data:/var/lib/mysql \
   # 日志目录
     -v /data/docker/mysql/logs:/var/log/mysql \
   # 配置目录
     -v /data/docker/mysql/conf:/etc/mysql/conf.d \
   # 自动重启
     --restart unless-stopped \
   # 版本号tag
     mysql:8.0
   ```

2. 查看状态

   ```shell
   # 查看运行状态
   docker ps
   
   # 查看启动日志（重点看有没有权限或初始化错误）
   docker logs tlias-mysql --tail 100
   ```

3. 验证

```shell
# 进入 MySQL 容器，登录 MySQL，查看数据库
docker exec -it tlias-mysql mysql -uroot -p -e "SHOW DATABASES;"

# 输出 exit 退出 MySQL，再输一次退出容器
```


## (4)配置宿主机连接权限

1. 可以发现容器外部不能连接容器内的 MySQL

   ```shell
   # 测试连接（在宿主机执行）
   mysql -h 127.0.0.1 -P 3306 -u root -p
   
   # 报错
   # ERROR 1130 (HY000): Host '172.17.0.1' is not allowed to connect to this MySQL server
   ```

2. 创建开发用户

   ```shell
   # 进入 MySQL 容器内部
   docker exec -it tlias-mysql mysql -uroot -p
   
   -- 创建开发用户（% 表示允许任意 IP 访问）
   CREATE USER 'dev'@'%' IDENTIFIED BY '123456';
   
   -- 授权全部数据库
   GRANT ALL PRIVILEGES ON *.* TO 'dev'@'%' WITH GRANT OPTION;
   -- 备注: 因为是学习阶段，*.* 这个权限比较大，也可以调成  数据库A.*
   
   -- 刷新
   FLUSH PRIVILEGES;
   
   -- 查看所有 MySQL 用户以及可以访问的 IP
   select user, host from mysql.user;
   ```

3. 测试

   ```shell
   # 在宿主机测试连接
   mysql -h 127.0.0.1 -P 3306 -u dev -p
   
   # 查看数据库
   SHOW DATABASES;
   ```



# 4、配置后端容器
## (1)停止原来的后端服务
防止占用端口

```shell
# 停止 systemd 服务
sudo systemctl stop tlias # 替换为原来的后端服务名

# 关闭开机自启
sudo systemctl disable tlias

# 确认已停止
sudo systemctl status tlias   # 看到 inactive/dead 即可
```


## (2)创建标准目录结构
1. 目录结构

   ```text
   /data/docker
   ├── apps
   │   ├── tlias-backend
   │   │     ├── Dockerfile
   │   │     ├── app.jar
   │   │     └── logs
   │   │
   │   └── future-service
   │
   ├── mysql  
   │   ├── conf  
   │   ├── data
   │   └── logs
   │
   ├── nginx # 以后可以专门用 infrastructure 文件夹放 mysql,nginx,redis 等等基础设施
   │   ├── Dockerfile
   │   ├── conf
   │   ├── html   (vue build)
   │   └── logs
   │
   └── docker-compose.yml（后面统一编排）
   ```

2. 创建目录

   ```shell
   # 切到数据盘 Docker 根目录
   cd /data/docker
   
   # 创建后端应用目录 + 日志目录
   mkdir -p apps/tlias-backend/logs
   ```


## (3)准备 jar 包
1. 修改 `application.yaml`
   * **确保application.yaml的数据库username、password = MySQL 容器**

   * 原来

   ```yaml
   spring:
     # 数据源配置
     datasource:
       driver-class-name: com.mysql.cj.jdbc.Driver
       # 本地编译器测试/Linux裸机部署
       url: jdbc:mysql://127.0.0.1:3306/tlias?useSSL=false&serverTimezone=Asia/Shanghai&allowPublicKeyRetrieval=true
       username: root
       password: 123456
   ```
   
   * 现在
   
   ```yaml
   spring:
     datasource:
        # 容器名 tlias-mysql
       url: jdbc:mysql://tlias-mysql:3306/tlias?useSSL=false&serverTimezone=Asia/Shanghai&allowPublicKeyRetrieval=true
       username: dev
       password: 123456
   ```

2. 确保 pom.xml 中有 `spring-boot-maven-plugin`, 并且设置打出来的 Jar 包叫做`app.jar`

   ```xml
        <build>
            <!--   指定jar包名称 -->
            <finalName>app</finalName>
   
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

3. IDEA右侧Maven 面板 → 当前项目的Lifecycle → 点击clean → 再点击package

   * 或者当前项目目录，终端运行 `mvn clean package`

   ![](/assets/images/技术教程类/项目部署/Linux部署SpringBoot项目/01-MavenCleanPackage.png "")

4. 找到项目的 `target`文件夹，里面有`app.jar`,就是包含依赖、可在JRE下运行的FatJar包

   * 另外一个`.jar.origin`文件不包含依赖，不管他

5. 上传 jar 包
   
   * 先确保权限
   
   ```shell
   # 改所属者为普通用户，这样 FinalShell 可以直接上传避免权限问题
   sudo chown -R student:student /data
   # 改权限
   sudo chmod -R 755 /data
   ```
   
   * 然后上传 jar 包， `/data/docker/apps/tlias-backend/app.jar`


## (4)写入 Dockerfile

```shell

cd /data/docker/apps/tlias-backend

# `cat > ` 不如 tee 好用
sudo tee Dockerfile << 'EOF'
# 使用 Java 21 运行环境（轻量 Alpine 版本）
FROM eclipse-temurin:21-jre-alpine

# 容器内工作目录
WORKDIR /app

# 创建非 root 用户（安全隔离）
RUN addgroup -S spring && adduser -S spring -G spring

# 拷贝 jar 包（注意必须是当前目录文件）
COPY app.jar app.jar

# 创建日志目录（可选）
RUN mkdir -p logs && chown -R spring:spring /app

# 切换用户运行
USER spring

# 暴露后端端口（SpringBoot端口）
EXPOSE 8081

# 启动命令
ENTRYPOINT ["java", "-jar", "app.jar"]

# Spring profile（可按需修改/删除）
CMD ["--spring.profiles.active=prod"]
EOF
```


## (5)创建网络

* 踩坑: AI的教程创建网络、修改application.yaml，所以又折腾了不少时间才解决

```shell
# 创建网络
docker network create tlias-net
# 把 MySQL 加入网络
docker network connect tlias-net tlias-mysql
```


## (6)构建镜像 + 启动容器

```shell
# 切换目录到有 Dockerfile 的地方
cd /data/docker/apps/tlias-backend
# 构建镜像
docker build -t tlias-backend:1.0 .
# 启动容器，并把后端容器加入网络
docker run -d \
  --name tlias-backend \
  --network tlias-net \
  -p 8081:8081 \
  -v /data/docker/apps/tlias-backend/logs:/app/logs \
  --restart unless-stopped \
  tlias-backend:1.0
```

* 再次提醒: 如果构建镜像出现网络问题，直接关掉虚拟机，**从VirtualBox的虚拟机界面重新启动虚拟机**(跟命令`reboot`重启的效果不一样)


## (7)验证网络 + 容器

1. 查看网络、容器

   ```shell
   # 查看网络包含的容器
   docker network inspect tlias-net
   # 查看所有容器
   docker ps -a
   # 查看运行日志
   docker logs tlias-backend
   ```

2. 访问接口

   ```shell
   # 验证接口
   curl localhost:8081
   # 需要登录
   
   # 在 Terminal 访问后端，进行登录
   curl -X POST http://localhost:8081/login \
   -H "Content-Type: application/json" \
   -d '{"username":"linchong","password":"123456"}'
   # 复制返回的 token
   
   # 访问后端数据
   # 方法1, 直接把 token 带在请求头
   curl http://localhost:8081/depts \
   -H "token:我是token"
   
   # 方法2，用变量
   # 1. 把 token 存变量
   TOKEN="我是TOKEN" 
   # 2. 请求时带上 
   curl http://localhost:8081/depts \
   -H "token: $TOKEN"
   
   # 这样就算跑通了后端
   ```

3. 注意
   * 重新上传 app.jar, 需要重新构建镜像，新构建的会覆盖旧的镜像
   * 构建镜像后需要删除原容器(如果有, `docker stop tlias-backend`, `docker rm tlias-backend`)



# 5、配置前端容器
## (1)停止原服务

```shell
# 停止 systemd 服务
sudo systemctl stop nginx # 替换为原来的前端服务名

# 关闭开机自启
sudo systemctl disable nginx

# 确认已停止
sudo systemctl status nginx   # 看到 inactive/dead 即可
```


## (2)创建前端目录结构

```shell
# 创建目录
mkdir -p /data/docker/nginx/{conf,html,logs}

# 设置权限
sudo chown -R student:student /data/docker/nginx
sudo chmod -R 755 /data/docker/nginx

# 把已有的 Vue 构建文件复制进去
sudo cp -a /opt/nginx/html/tlias-management-frontend/. /data/docker/nginx/html/tlias-management-frontend/
```

* 注: 或者直接 FinalShell 上传 vue 项目的构建文件夹，可以参考文章<u>[Linux配置Nginx-Vue打包](/技术教程类/项目部署/Linux配置Nginx-Vue打包.md)</u>的`Vue项目打包`部分


## (3)创建 Nginx 配置文件

* 记得把这里面出现的 `tlias` 换成自己的项目名

```shell
sudo tee /data/docker/nginx/conf/nginx.conf << 'EOF'
# worker 进程数（简单用1）
worker_processes  1;

events {
worker_connections 1024;
}

http {
    
    include       /etc/nginx/mime.types;  # 容器内路径
    default_type  application/octet-stream;

    # 日志（容器内路径，建议后面做 volume 挂载）
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log main;
    error_log   /var/log/nginx/error.log warn;

    # 性能优化
    sendfile        on;
    keepalive_timeout 65;
    client_max_body_size 100m;

    # gzip
    gzip on;
    gzip_types text/plain text/css application/json application/javascript;

    server {
        listen 80;
        server_name localhost;

        # ================= 前端 =================
        location / {
            # ⚠️ 容器内路径（必须配合 volume）
            root /usr/share/nginx/html/tlias-management-frontend;
            index index.html;

            # Vue history 模式
            try_files $uri $uri/ /index.html;
        }

        # ================= 后端 =================
        location ^~ /api/ {
            # 去掉 /api 前缀
            rewrite ^/api/(.*)$ /$1 break;

            # 容器名通信（关键）
            proxy_pass http://tlias-backend:8081;

            # 透传请求头
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;

            # 超时
            proxy_connect_timeout 60s;
            proxy_send_timeout 60s;
            proxy_read_timeout 120s;
        }

        # 状态页
        location /nginx_status {
            stub_status on;
            access_log off;
        }

        # 错误页
        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
            root /usr/share/nginx/html;
        }
    }
}
EOF
```

## (4)创建前端 Dockerfile

* 记得把这里面出现的 `tlias` 换成自己的项目名

```shell
sudo tee /data/docker/nginx/Dockerfile << 'EOF'
# 基础镜像（官方 nginx）
FROM nginx:1.28-alpine

# 删除默认站点配置（避免冲突）
RUN rm -rf /etc/nginx/conf.d/default.conf

# 拷贝自定义 nginx 主配置
# 宿主机：/data/docker/nginx/conf/nginx.conf
# 容器内：/etc/nginx/nginx.conf
COPY conf/nginx.conf /etc/nginx/nginx.conf

# 拷贝前端构建产物
# 宿主机：/data/docker/nginx/html/tlias-management-frontend
# 容器内：/usr/share/nginx/html/tlias-management-frontend
COPY html/tlias-management-frontend /usr/share/nginx/html/tlias-management-frontend

# 创建日志目录并赋权（nginx 运行用户是 nginx）
RUN mkdir -p /var/log/nginx && \
    chown -R nginx:nginx /var/log/nginx /usr/share/nginx/html

# 暴露 80 端口（仅声明）
EXPOSE 80

# 前台运行 nginx（容器必须前台进程）
CMD ["nginx", "-g", "daemon off;"]
EOF
```


## (5)构建镜像 + 启动容器

```shell
cd /data/docker/nginx

# 构建镜像（注意最后有个 . 表示当前目录）
docker build -t tlias-frontend:1.0 .

# 启动容器(无注释)
docker run -d \
  --name tlias-frontend \
  --network tlias-net \
  -p 80:80 \
  -v /data/docker/nginx/logs:/var/log/nginx \
  --restart unless-stopped \
  tlias-frontend:1.0

# 启动容器(有注释)
docker run -d \
  --name tlias-frontend \          # 前端容器命名统一
  --network tlias-net \            # 和 backend 同网络
  -p 80:80 \                       # 虚拟机80 → 容器80
  -v /data/docker/nginx/logs:/var/log/nginx \  # 日志持久化
  --restart unless-stopped \       # 推荐策略,手动 stop 后不会自动重启
  tlias-frontend:1.0
```


## (6)验证状态

1. 命令行

   ```shell
   # 查看状态
   docker ps
   
   # 查看日志
   docker logs tlias-frontend
   
   # 由 Nginx 转发访问后端
   # 或者不带端口号
   curl http://localhost:80/api/xxx \
   -H "token:我是token"
   # 预期可以成功拿到 JSON 数据, 跟 curl localhost:8081 的效果一样
   ```

2. 本地浏览器(Edge)

   * 访问 localhost:8080, 经由 VirtualBox 的端口转发规则，转发到虚拟机内的 Nginx 80 端口
   * 然后成功在 Edge 跑通项目
   * 注: 端口转发规则配置可以参考文章<u>[Linux配置Nginx-Vue打包](/技术教程类/项目部署/Linux配置Nginx-Vue打包.md)</u>的`端口转发`部分


## (7)跑不通的处理方法

1. 常见问题(**前端没连接后端**)

   * `curl http://localhost:80/api/xxx` 出现 Nginx 的错误界面 `An error occurred`  

   * `docker logs tlias-frontend` 重复输出 `[emerg] host not found` 和 `ready for start up`

   * 浏览器访问项目，可以打开登录界面，但点击登录时报错(`接口访问异常`之类的)

2. 确保容器启动的时候

   * 顺序是底层 → 后端 → 前端
   * 都带上`--restart unless-stopped`，防止中途为了刷新虚拟机网络、关闭虚拟机、重启后有底层/后端容器没启动
   * 启动时没指定开机自启策略 => 手动指定 `docker update --restart=unless-stopped tlias-backend`，或者停止、删除容器重建
   * 指定网络 `--network tlias-net`
   * 或者手动查看`docker network inspect tlias-net`,把没加进来的加进来 `docker network connect tlias-net 容器名`

3. 简单处理

   ```shell
   # 暂停前端容器
   docker stop tlias-frontend
   
   # 重启后端容器
   docker restart tlias-backend
   
   # 等一会儿
   # 重启前端容器
   docker restart tlias-frontend
   
   # 再来 curl localhost:80 或者浏览器访问
   ```
   
4. 暴力处理

   ```shell
   # 暂停并删除前端容器
   docker rm -f tlias-frontend
   
   # 然后重建容器
   # 并且还可以把后端容器也删了，后端、前端再依次重建(我就是这样搞出来的😂)
   ```

5. 小改 nginx.conf

   * `cd /data/docker/nginx/conf` 然后 `sudo gedit nginx.conf` 打开、修改配置

   ```nginx configuration
   # 看到这里
   location ^~ /api/ {
      # 去掉 /api 前缀
      rewrite ^/api/(.*)$ /$1 break;
   
      # 容器名通信（关键）
      # 在最后加1个正斜杠/
      # rewrite 和这个 / 的作用都是去除 /api 前缀，这里就双重保险
      proxy_pass http://tlias-backend:8081/; 
       
      # ...
   }
   ```

   * 然后 `cd /data/docker/nginx`
   * 重新构建镜像 `docker build -t tlias-frontend:1.0 .`
   * 再删除容器、重建



# 6、DockerCompose 编排

* 上面的操作是跑通单个容器，现在来整合到一起

## (1)处理旧容器

```shell
# 停止旧容器
docker stop tlias-frontend tlias-backend tlias-mysql
# 查看状态
docker ps
# 删除旧容器（不会删除镜像、数据卷、宿主机文件）
docker rm -f tlias-frontend tlias-backend tlias-mysql
# 应该看不到这些容器了
docker ps -a
```


## (2)修改后端

* 再次修改 `application.yaml` 的数据源URL

```yaml
# 把容器名 tlias-mysql 改为服务名 mysql
url: jdbc:mysql://mysql:3306/tlias?useSSL=false&serverTimezone=Asia/Shanghai&allowPublicKeyRetrieval=true
```

* 然后重新 clean、package 成 app.jar
* 上传(`/data/docker/apps/tlias-backend/app.jar`)
* 重新在 `/data/docker/apps/tlias-backend` 目录构建镜像(`docker build -t tlias-backend:1.0 .`)
* 验证镜像存在 `docker images`


## (3)创建 `docker-compose.yml`

```shell
cd /data/docker 

# 创建并写入文件
cat > docker-compose.yml << 'EOF'
services:

  # ================= MySQL =================
  mysql:
    image: mysql:8.0

    # 容器名称（方便 docker ps 查看）
    container_name: tlias-mysql

    # 容器异常退出自动重启
    restart: unless-stopped

    # MySQL 环境变量
    # ⚠️ 注意：
    # 由于 data 目录已存在，此密码不会重新初始化
    # 当前实际密码仍是你之前创建数据库时的密码
    environment:
      MYSQL_ROOT_PASSWORD: 123456

    # 端口映射
    ports:
      - "3306:3306"

    # 数据持久化
    volumes:
      # MySQL 数据文件
      - ./mysql/data:/var/lib/mysql

      # MySQL 日志
      - ./mysql/logs:/var/log/mysql

    # 加入统一网络
    networks:
      - tlias-net



  # ================= 后端 =================
  backend:
    image: tlias-backend:1.0

    # 容器名称
    container_name: tlias-backend

    # 异常自动重启
    restart: unless-stopped

    # 端口映射
    ports:
      - "8081:8081"

    # 日志目录挂载
    volumes:
      - ./apps/tlias-backend/logs:/app/logs

    # 启动顺序（仅顺序，不保证 mysql 已完全 ready）
    depends_on:
      - mysql

    # 同一网络
    networks:
      - tlias-net



  # ================= 前端 =================
  frontend:
    image: tlias-frontend:1.0

    # 容器名称
    container_name: tlias-frontend

    # 异常自动重启
    restart: unless-stopped

    # HTTP 端口
    ports:
      - "80:80"

    # nginx 日志挂载
    volumes:
      - ./nginx/logs:/var/log/nginx

    # 启动顺序
    depends_on:
      - backend

    # 同一网络
    networks:
      - tlias-net



# ================= 网络 =================
networks:
  # 之前用的 docker network create tlias-net 手动创建网络
  # 而 compose 默认由它来创建网络，想要接管这个网络
  # 于是需要标记为外部已有网络，compose 只使用，不管理
  tlias-net:
    external: true
EOF


# 查看该文件内容
cat docker-compose.yml
```


## (4)Compose 启动所有服务

```shell
cd /data/docker

# 后台启动所有服务
docker compose up -d

# 检查状态
docker compose ps
docker ps
```

跑通~~~

---

* 知乎链接
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
> <u>[软工.大二下.4月生活日记](https://zhuanlan.zhihu.com/p/2034919650602042004)</u>  
> <u>[2026.3月.读书笔记](https://zhuanlan.zhihu.com/p/2018960358434414913)</u>  
> <u>[Node.js切换版本](https://zhuanlan.zhihu.com/p/2027012596826416513)</u>  
> <u>[个人博客仓库](https://github.com/existed-name/Personal-Blogs)</u>  
