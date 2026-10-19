# 内网穿透-Cpolar安装使用

* 简介: 内网穿透可以把本地端口临时暴露到公网，让其他人能够访问
* 在<u>[ Cpolar 官网](https://www.cpolar.com)</u>注册登录, 来到首页( 不用管它的实名认证提醒 )
* 然后下载软件

  ![01.png](/assets/images/技术教程类/开发工具&插件/内网穿透/Cpolar/01.png)

* 下载下来的压缩包解压 → 运行里面的 `.msi` 安装器 → 选择安装位置 → 安装成功

  ![02.png](/assets/images/技术教程类/开发工具&插件/内网穿透/Cpolar/02.png)

* 左侧菜单栏 → `验证` 界面 → 复制自己的 Cpolar AuthToken

  ![03.png](/assets/images/技术教程类/开发工具&插件/内网穿透/Cpolar/03.png)

* 然后在安装目录、`cpolar.exe` 所在位置, 打开 cmd 命令行输入 `cpolar authtoken 我的AuthToken`

* 启动某个需要暴露到公网的服务, 然后在 `cpolar.exe` 所在位置输入命令 `cpolar http 服务的端口号` 就可以把这个端口暴露到公网了
* 命令行等待一段时间后会给出图形化界面地址( 如图的 127.0.0.1:4042, 可以看端口请求状态 )、公网地址, 然后测试能不能访问公网地址

  ![04.png](/assets/images/技术教程类/开发工具&插件/内网穿透/Cpolar/04.png)

  ![05.png](/assets/images/技术教程类/开发工具&插件/内网穿透/Cpolar/05.png)

* Ctrl + C 停止 Cpolar, 也可以在 Cpolar 官网登录后 → 左侧菜单栏 `状态` → 管理公网地址
* 注意
  - 切换网络可能影响 Cpolar
  - 免费版套餐每天会刷新暴露的公网地址
  - 免费版每分钟请求数量有限制, 如果暴露前端可能出现能加载网页标题、但是内容白屏的情况( 请求太多被限制了 )


