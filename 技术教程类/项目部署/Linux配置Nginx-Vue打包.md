# Linux配置Nginx+Vue打包

## 目录
- [配置清单](#一配置清单)
- [Vue项目打包](#二vue项目打包)
- [本地测试](#三本地测试)
  - [配置本地Nginx](#1配置本地nginx)
  - [部署前端](#2部署前端)
- [配置虚拟机](#四配置虚拟机)
  - [Nginx](#1nginx)
  - [部署前端](#2部署前端-1)
  - [端口转发](#3端口转发)

---

---

# (一)配置清单
1. VirtualBox7.2
   * 如果想方便复制粘贴命令，可参考文章<u>[虚拟机和主机共享剪贴板](https://zhuanlan.zhihu.com/p/1979949196330176615)</u>配置增强功能

2. Debian13

3. FinalShell4.6(上传文件)
   * 可参考文章:<u>[FinalShell连接虚拟机](/技术教程类/项目部署/FinalShell连接虚拟机.md)</u>

4. nginx-1.28.3.tar.gz

5. 系列文章: <u>[Linux部署SpringBoot项目](/技术教程类/项目部署/Linux部署SpringBoot项目.md)</u>


# (二)Vue项目打包

1. 自定义输出文件夹的名称
    * 可跳过，默认是dist文件夹,这里是为了区分不同前端项目
    * 注意dist文件夹会有颜色高亮，改成其他的名字就只是普通文件夹的颜色
    * 直接把build出来的dist文件夹重命名也能跑，但可能会跟文件夹内部有一点冲突
    * 来到`vite.config.js`，在`export default defineConfig`代码块里面添加代码

   ```js
     // 新增：自定义构建输出目录
     build: {
       // outDir: 'dist',
       outDir: 'tlias-management-frontend',   // 输出为这个名字，而不是默认的 dist
       emptyOutDir: true                      // 每次 build 前清空旧文件，避免旧文件残留
       // sourcemap: false          // 生产通常关闭
     }
   ```

2. `build`打包
   * 来到package.json → 点击`vite build`左边的绿色箭头

   ![](/assets/images/技术教程类/项目部署/Linux配置Nginx-Vue打包/01-ViteBuild.png "")
   
   * 然后看到前端项目根目录新出现的,就是目标文件夹
   
   ![](/assets/images/技术教程类/项目部署/Linux配置Nginx-Vue打包/02.png "")
   
   ![](/assets/images/技术教程类/项目部署/Linux配置Nginx-Vue打包/03-Dist.png "")


# (三)本地测试
## 1、配置本地Nginx
安装、配置、基本命令(启动/停止/重载配置)可参考这篇博客: <u>[Nginx基本使用](/技术教程类/开发工具&插件/Nginx/Nginx基本使用.md)</u>(这篇文章是早期写的，当时还不知道可以把前端打包文件放进去代理😅)


## 2、部署前端
1. 把build出来的前端文件夹放进nginx文件夹的`html`文件夹

2. 打开nginx文件夹的`conf/nginx.conf`配置文件
   * 这里用的IDEA修改
   * 不过IDEA还不认识这个文件类型，注释会变成`;`而不是`#`

   ![](/assets/images/技术教程类/项目部署/Linux配置Nginx-Vue打包/04.png "")

   * 需要下载相应插件(`Nginx Configuration`)

   ![](/assets/images/技术教程类/项目部署/Linux配置Nginx-Vue打包/05-DownloadNginxConfiguration.png "")

   * IDEA的Setting → Editor → File Type里面新增1个 `Nginx configuration file` 就可以成功支持Nginx语法了

   ![](/assets/images/技术教程类/项目部署/Linux配置Nginx-Vue打包/06.png "")

3. 修改配置
   * 把`http`代码块里面的`server`块替换掉

   ```nginx configuration
       server {
           listen       90; # 继续用 90 避开 80 没问题
           server_name  localhost;
   
           # 1. 增加上传限制，后端做文件上传时必用
           client_max_body_size 20m;
   
           # 2. 优化前端静态资源加载
           location / {
   #             root   dist;
               root   html/tlias-management-frontend; # 指向具体的项目目录
               index  index.html index.htm;
               # 核心：支持 Vue Router 的 History 模式
               try_files $uri $uri/ /index.html;
           }
   
           # 3. 规范的 API 转发
           location ^~ /api/ {
               # 显式重写路径：/api/login -> /login
               rewrite ^/api/(.*)$ /$1 break;
               proxy_pass http://localhost:8081;
   
               # 传递真实的客户端信息给后端（SpringCloud 架构中很重要）
               proxy_set_header Host $host;
               proxy_set_header X-Real-IP $remote_addr;
               proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           }
   
           # 4. 开启 Gzip 压缩（进阶建议：大厂必做）
           # 能极大提升前端首屏加载速度
           gzip on;
           gzip_types text/plain text/css application/json application/javascript;
   
           error_page   500 502 503 504  /50x.html;
           location = /50x.html {
               root   html;
           }
       }
   ```
   
   * 注意这里后端端口8081、Nginx代理前端端口90，根据自己的情况设置就行
   * 如果有多个前端文件，就用多个`server`块，`location`块里面指定`html/前端项目名称`

4. 然后重载配置，启动Nginx，浏览器访问`localhost:90`,看到前端界面、跑通，说明打出来的包、`nginx.conf`正确



# (四)配置虚拟机
## 1、Nginx
1. 下载Nginx的源码包
   * 来到<u>[官网下载界面](https://nginx.org/en/download.html)</u> → `Stable version` → 中间这个`nginx-1.28.3`
   * 右边那个`nginx/Windows-1.28.3`是用在Windows上的

   ![](/assets/images/技术教程类/项目部署/Linux配置Nginx-Vue打包/07-DownloadNginx.png "")

2. 上传压缩包
   * 比如我放在`/home/student/Programming/Web-Server/Nginx/nginx-1.28.3.tar.gz`
   * 根据个人情况换目录

3. 安装编译依赖

   ```shell
   sudo apt update
   sudo apt install -y build-essential libpcre2-dev libssl-dev zlib1g-dev libgd-dev curl
   ```

4. 创建目录并解压

   ```shell
   sudo mkdir -p /opt/nginx-1.28.3
   sudo tar -xzf /home/student/Programming/Web-Server/Nginx/nginx-1.28.3.tar.gz -C /opt/nginx-1.28.3 --strip-components=1
   ```

5. 编译安装(压缩包里面是C语言源码)
   * 注意`/opt/nginx-1.28.3`是源码，这里新建的`/opt/nginx`是安装后的文件夹，**之后用的`/opt/nginx`**

   ```shell
   cd /opt/nginx-1.28.3
   
   sudo ./configure \
     --prefix=/opt/nginx \
     --sbin-path=/opt/nginx/sbin/nginx \
     --conf-path=/opt/nginx/conf/nginx.conf \
     --error-log-path=/var/log/nginx/error.log \
     --http-log-path=/var/log/nginx/access.log \
     --pid-path=/var/run/nginx.pid \
     --lock-path=/var/run/nginx.lock \
     --http-client-body-temp-path=/var/cache/nginx/client_temp \
     --http-proxy-temp-path=/var/cache/nginx/proxy_temp \
     --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp \
     --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp \
     --http-scgi-temp-path=/var/cache/nginx/scgi_temp \
     --with-http_ssl_module \
     --with-http_v2_module \
     --with-http_realip_module \
     --with-http_gzip_static_module \
     --with-http_stub_status_module \
     --with-stream \
     --with-stream_ssl_module
   
   sudo make -j$(nproc)
   sudo make install
   ```

6. 创建必要目录和权限

   ```shell
   sudo mkdir -p /var/cache/nginx/client_temp /var/cache/nginx/proxy_temp /var/cache/nginx/fastcgi_temp /var/cache/nginx/uwsgi_temp /var/cache/nginx/scgi_temp /var/log/nginx
   sudo chown -R www-data:www-data /var/cache/nginx /var/log/nginx
   sudo mkdir -p /opt/nginx/conf
   ```

7. 创建 systemd 服务（开机自启）

   ```shell
   sudo tee /etc/systemd/system/nginx.service > /dev/null <<EOF
   [Unit]
   Description=nginx - high performance web server
   Documentation=http://nginx.org/en/docs/
   After=network-online.target remote-fs.target nss-lookup.target
   Wants=network-online.target
   
   [Service]
   Type=forking
   PIDFile=/var/run/nginx.pid
   ExecStart=/opt/nginx/sbin/nginx -c /opt/nginx/conf/nginx.conf
   ExecReload=/opt/nginx/sbin/nginx -s reload
   ExecStop=/bin/kill -s QUIT \$MAINPID
   PrivateTmp=true
   
   [Install]
   WantedBy=multi-user.target
   EOF
   ```

8. 启动并验证

   ```shell
   sudo systemctl daemon-reload
   sudo systemctl enable nginx
   sudo systemctl start nginx
   sudo systemctl status nginx # 应该会输出 active
   ```

9. 验证安装
   ```shell
   # 版本号
   /opt/nginx/sbin/nginx -v
   # 应该会输出 HTTP/1.1 200 OK 等等一堆信息
   curl -I http://127.0.0.1
   ```


## 2、部署前端
* 注意，**用的`/opt/nginx`而不是源码`/opt/nginx`**

1. 上传前端文件，`/opt/nginx/html/tlias-management-frontend`

2. 备份原配置

   ```shell
   # 备份原配置（安全）
   sudo cp /opt/nginx/conf/nginx.conf /opt/nginx/conf/nginx.conf.bak
   
   # 编辑主配置文件
   sudo gedit /opt/nginx/conf/nginx.conf
   ```

3. 用以下代码覆盖原来的`nginx.conf`
   * 注意**这里监听的端口号是80**,跟刚刚本地Nginx测试的不一样

   ```nginx configuration
   # user  nobody;                    # 默认用户，生产建议改成 www-data 或 nginx
   worker_processes  1;              # 学习阶段用 1 即可，生产根据 CPU 核数调整
   
   events {
       worker_connections  1024;     # 单个 worker 最大并发连接数
   }
   
   http {
       include       mime.types;     # 加载 MIME 类型映射（让 nginx 正确识别 css/js 等文件）
       default_type  application/octet-stream;
   
       # ==================== 全局优化设置 ====================
       log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                         '$status $body_bytes_sent "$http_referer" '
                         '"$http_user_agent" "$http_x_forwarded_for"';
   
       access_log  /var/log/nginx/access.log  main;   # 访问日志
       error_log   /var/log/nginx/error.log  warn;    # 错误日志（warn 级别，避免太多噪音）
   
       sendfile        on;           # 启用零拷贝，提升静态文件发送性能
       keepalive_timeout  65;        # 保持连接超时
       client_max_body_size 100m;    # 全局上传大小限制（与后端匹配）
   
       gzip  on;                     # 开启 Gzip 压缩
       gzip_types text/plain text/css application/json application/javascript;
   
       # ==================== 你的主 server 块 ====================
       server {
           listen       80;                  # 学习/测试用 80，生产建议配合端口转发或直接用 80
           server_name  localhost;           # 生产环境改成你的域名
   
           # ====================== 前端静态资源 ======================
           location / {
               root   /opt/nginx/html/tlias-management-frontend;   # ← 改成你实际的前端 dist 目录
               index  index.html index.htm;
   
               # Vue/Vite/React Router History 模式关键配置
               # 未找到文件时全部回落到 index.html（解决刷新 404）
               try_files $uri $uri/ /index.html;
           }
   
           # ====================== 后端 API 代理 ======================
           # 推荐写法：使用 ^~ 提高优先级，避免被其他 location 干扰
           location ^~ /api/ {
               # 重写路径：前端请求 /api/login → 后端实际收到 /login（更干净）
               rewrite ^/api/(.*)$ /$1 break;
   
               proxy_pass http://127.0.0.1:8081;
   
               # 传递真实客户端信息（Spring Boot 常用）
               proxy_set_header Host $host;
               proxy_set_header X-Real-IP $remote_addr;
               proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
               proxy_set_header X-Forwarded-Proto $scheme;
   
               # 超时设置（大文件上传友好）
               proxy_connect_timeout 60s;
               proxy_send_timeout 60s;
               proxy_read_timeout 120s;
           }
   
           # 可选：Nginx 状态监控页面（访问 /nginx_status 查看状态）
           location /nginx_status {
               stub_status on;
               access_log off;
           }
   
           # 错误页面
           error_page   500 502 503 504  /50x.html;
           location = /50x.html {
               root   html;
           }
       }
   
       # 如果以后要加第二个项目，推荐新增另一个 server 块（而非在同一个 server 里加 location）
       # server {
       #     listen 80;
       #     server_name admin.localhost;
       #     ...
       # }
   }
   ```

4. 保存退出后，测试并重启

   ```shell
   # 检查配置有没有错
   sudo /opt/nginx/sbin/nginx -t # nginx: syntax is ok, test is successful
   # 重启
   sudo systemctl restart nginx
   # 查看服务状态
   sudo systemctl status nginx
   ```


## 3、端口转发
1. 可以发现，虽然已经启动了后端(端口8081)、前端(代理端口80，转发到后端8081)，但是本地Edge访问`localhost:80`没反应
   * 原因是8081、80是虚拟机的端口，而主机不能直接访问虚拟机端口(VirtualBox是这样的)
   * 而端口转发只配置了 SSH（主机2222 → 虚拟机22），没有配置 Web 端口（主机端口 → 虚拟机80）

2. VirtualBox左侧导航栏 → 网络 → NAT网络 → 给自己创建的`MyNatNetwork`的端口转发，新增规则
   * 名称: HTTP
   * 主机IP: 留空，或者127.0.0.1
   * 主机端口: 8080
   * 子系统/客户机IP: 自己虚拟机的IP(`ip addr show`查看)
   * 子系统/客户机端口: 80 = nginx.conf的server监听端口

   ![](/assets/images/技术教程类/项目部署/Linux配置Nginx-Vue打包/08-PortForwarding.png "")

3. 然后关闭虚拟机、从VirtualBox重新进入虚拟机,让端口转发规则生效
   ```shell
   # 查看状态
   sudo systemctl status tlias # 后端服务
   sudo systemctl status mysql
   sudo systemctl status nginx
   
   # 如果不是 active(running) 就启动
   sudo systemctl start tlias
   sudo systemctl start mysql
   sudo systemctl start nginx
   ```
   
4. Edge访问`localhost:8080`，成功跑通项目🥳

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
