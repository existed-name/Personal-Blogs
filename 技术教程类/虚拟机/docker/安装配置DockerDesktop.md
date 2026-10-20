# 安装配置DockerDesktop

---

---

## 简介

> Docker Desktop 是 Docker 官方桌面端容器开发环境，集成了 Docker Engine、CLI、Compose、单机 Kubernetes 和图形界面，解决了本地快速起容器/多容器服务、查看状态与日志、exec 进容器、管理镜像/卷/网络，以及端口映射、卷挂载、网络配置等常见问题

* 用途: 本地部署项目、基础设施
* 笔者配置：win11 + wsl2 
* 如果是Debian/Ubuntu虚拟机（包括wsl）安装docker, 可以参考[这篇文章的 `2、Docker安装配置` 章节](/技术教程类/项目部署/SpringBoot项目部署-从裸机部署到Docker容器化.md#2docker安装配置)

## 安装 wsl

* 在 Windows 上，Docker Desktop 底层依赖 WSL 2 或 Hyper-V 来运行 Linux 容器，默认推荐 WSL 2 后端，性能和文件系统兼容性更好
* 详见<u>[安装WSL](/技术教程类/虚拟机/wsl/安装WSL.md)</u>

## 安装 Docker Desktop

1. 来到<u>[官网下载界面](https://www.docker.com/products/docker-desktop/)</u>(可能因为服务器在国外，加载有一点慢)

    ![01-download.png](../../../assets/images/%E6%8A%80%E6%9C%AF%E6%95%99%E7%A8%8B%E7%B1%BB/%E8%99%9A%E6%8B%9F%E6%9C%BA/docker/%E5%AE%89%E8%A3%85%E9%85%8D%E7%BD%AEDockerDesktop/01-download.png)

2. 把 Docker Desktop / WSL2 需要的 Windows 底层功能打开, 这一步需要管理员命令行 → win + x → `终端(管理员)`

    ```shell
    # 启用 WSL 功能
    # /online                 操作当前正在运行的系统
    # /enable-feature         启用一个 Windows 功能
    # /featurename:...        指定要启用的功能：Windows Subsystem for Linux
    # /all                    连同依赖的父功能一起启用
    # /norestart              启用后不自动重启电脑
    dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
    
    # 启用“虚拟机平台”功能，WSL2 依赖它
    # 参数含义同上，只是功能名换成 VirtualMachinePlatform
    dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
    ```

    ![02.png](../../../assets/images/%E6%8A%80%E6%9C%AF%E6%95%99%E7%A8%8B%E7%B1%BB/%E8%99%9A%E6%8B%9F%E6%9C%BA/docker/%E5%AE%89%E8%A3%85%E9%85%8D%E7%BD%AEDockerDesktop/02.png)

3. 安装 Docker Desktop

    ![03.png](../../../assets/images/%E6%8A%80%E6%9C%AF%E6%95%99%E7%A8%8B%E7%B1%BB/%E8%99%9A%E6%8B%9F%E6%9C%BA/docker/%E5%AE%89%E8%A3%85%E9%85%8D%E7%BD%AEDockerDesktop/03.png)
    ![04.png](../../../assets/images/%E6%8A%80%E6%9C%AF%E6%95%99%E7%A8%8B%E7%B1%BB/%E8%99%9A%E6%8B%9F%E6%9C%BA/docker/%E5%AE%89%E8%A3%85%E9%85%8D%E7%BD%AEDockerDesktop/04.png)

4. 进入 Docker Desktop

    ![05.png](../../../assets/images/%E6%8A%80%E6%9C%AF%E6%95%99%E7%A8%8B%E7%B1%BB/%E8%99%9A%E6%8B%9F%E6%9C%BA/docker/%E5%AE%89%E8%A3%85%E9%85%8D%E7%BD%AEDockerDesktop/05.png)

    * 跳过邮箱设置

    ![06-skip-email-settings.png](../../../assets/images/%E6%8A%80%E6%9C%AF%E6%95%99%E7%A8%8B%E7%B1%BB/%E8%99%9A%E6%8B%9F%E6%9C%BA/docker/%E5%AE%89%E8%A3%85%E9%85%8D%E7%BD%AEDockerDesktop/06-skip-email-settings.png)
    ![07.png](../../../assets/images/%E6%8A%80%E6%9C%AF%E6%95%99%E7%A8%8B%E7%B1%BB/%E8%99%9A%E6%8B%9F%E6%9C%BA/docker/%E5%AE%89%E8%A3%85%E9%85%8D%E7%BD%AEDockerDesktop/07.png)

## 配置

* 右上角齿轮图标，进入设置 → 左侧侧边栏，选择 `Docker Engine`, 修改配置

    ![08-docker-engine-config.png](../../../assets/images/%E6%8A%80%E6%9C%AF%E6%95%99%E7%A8%8B%E7%B1%BB/%E8%99%9A%E6%8B%9F%E6%9C%BA/docker/%E5%AE%89%E8%A3%85%E9%85%8D%E7%BD%AEDockerDesktop/08-docker-engine-config.png)

    ```json
    {
      "builder": {
        "gc": {
          // 启用 BuildKit 构建缓存的垃圾回收
          "enabled": true,
    
          // 构建缓存默认保留约 20GB，超出后按策略清理
          "defaultKeepStorage": "20GB"
        }
      },
    
      // 关闭 Docker 实验性功能
      "experimental": false,
    
      // Docker Hub 镜像加速站列表
      // 拉取官方镜像时优先尝试这些地址
      "registry-mirrors": [
        "https://docker.m.daocloud.io",
        "https://docker.unsee.tech",
        "https://docker.1panel.live",
        "https://hub.rat.dev"
      ]
    }
    ```
  
* 由于 json 不支持注释，如果保存失败或者报错，可以切换为下面这段没有注释的配置

    ```json
    {
      "builder": {
        "gc": {
          "defaultKeepStorage": "20GB",
          "enabled": true
        }
      },
      "experimental": false,
      "registry-mirrors": [
        "https://docker.m.daocloud.io",
        "https://docker.unsee.tech",
        "https://docker.1panel.live",
        "https://hub.rat.dev"
      ]
    }
    ```
  
* 把 Docker 存储数据设置到 D 盘，它默认在 `C:\Users\用户名\AppDataLocalDocker\wsl`
* 依然是设置界面 → 左侧侧边栏，`Resources` 界面 → `Disk Image Location` 那里进行设置, 比如设置为 `D\某个文件夹\data` 它会自动补上自己的文件夹 `DockerDesktopWSL`

    ![09.png](../../../assets/images/%E6%8A%80%E6%9C%AF%E6%95%99%E7%A8%8B%E7%B1%BB/%E8%99%9A%E6%8B%9F%E6%9C%BA/docker/%E5%AE%89%E8%A3%85%E9%85%8D%E7%BD%AEDockerDesktop/09.png)

* 之前我跟着 ai 的操作，在命令行输了一堆命令: 导出 C 盘数据、注销 C 盘虚拟磁盘、导入 D 盘目录、搜索确认活跃的 ext4.vhdx 虚拟磁盘文件...
* 结果过了 2 个多月，某次 Docker/wsl 内存爆了，才发现迁移命令只适用于旧版 Docker，新版 Docker Desktop 直接去 `Resources` 界面设置数据存放目录就行了
* 这里就不再展示那一堆命令了



