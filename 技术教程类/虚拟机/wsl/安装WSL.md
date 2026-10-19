# 安装 WSL

---

## 简介

> WSL（Windows Subsystem for Linux） 是 Windows 内置的轻量级 Linux 兼容环境，无需虚拟机或双系统即可运行完整 GNU/Linux 发行版并与 Windows 无缝集成，其 WSL 2 基于轻量虚拟化提供真实 Linux 内核和完整系统调用兼容性，是当前推荐版本

* 用途: docker desktop 默认依赖 wsl 来运行 Docker 容器, redis 也需要跑在 Linux 环境上面

## 安装

1. win + r 输入 cmd 进入命令行
2. 输入 ` wsl --install -d Ubuntu` 安装 ubuntu 发行版内核的 WSL，这里默认安到 C 盘，之后迁移到 D 盘
3. 等待...

    ![01.png](/assets/images/技术教程类/虚拟机/wsl/01.png)
    ![02.png](/assets/images/技术教程类/虚拟机/wsl/02.png)
    ![03.png](/assets/images/技术教程类/虚拟机/wsl/03.png)
    ![04.png](/assets/images/技术教程类/虚拟机/wsl/04.png)

4. 设置用户名、密码, 然后会自动进入

    ![05.png](/assets/images/技术教程类/虚拟机/wsl/05.png)

## 迁移到 D 盘

```shell
# 1. 关闭 WSL
wsl --shutdown

# 2. 导出 Ubuntu 到 D 盘
wsl --export Ubuntu "D:\Users\Programming\Virtual-Mechine\WSL\Ubuntu.tar"
# 目标文件位置 = 目标文件夹 + Ubuntu.tar, 这里是我本地的文件路径, 替换为自己的路径

# 3. 注销原 Ubuntu
wsl --unregister Ubuntu

# 4. 导入到 D 盘(把 .tar 解包)
wsl --import Ubuntu "D:\Users\Programming\Virtual-Mechine\WSL\Ubuntu"
"D:\Users\Programming\Virtual-Mechine\WSL\Ubuntu.tar" --version 2

# 5. 删除 tar 包（省空间）
Remove-Item "D:\Users\Programming\Virtual-Mechine\WSL\Ubuntu.tar"
```

![06-move-to-d-disk.png](/assets/images/技术教程类/虚拟机/wsl/06-move-to-d-disk.png)

* 然后把默认登录用户从 root 改成自己的用户名

    ```shell
    # 以 root 身份进入 WSL（绕过 root 无密码、su 不可用的问题）
    wsl -d Ubuntu -u root
    
    # 写入默认用户配置（此步需要 root 权限）
    echo -e "[user]\ndefault=自己的用户名" > /etc/wsl.conf
    
    # 退出 WSL（注意：wsl --shutdown 不要在这里执行）
    exit
    
    # ===== 回到了 Windows PowerShell 或者 cmd =====
    
    # 关闭 WSL 使配置生效（必须在 Windows 侧执行）
    wsl --shutdown
    
    # 重新进入并验证默认用户
    wsl -d Ubuntu
    
    # 验证, 应输出自己的用户名
    whoami
    ```

* 后续 `wsl -d ubuntu` 进入 WSL 即可

