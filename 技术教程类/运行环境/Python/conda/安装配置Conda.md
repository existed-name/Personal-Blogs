# 安装配置Conda

---

## 简介
    
* conda 是 python 包/依赖管理器，常用于复杂数学计算、复杂数据分析（比如选修课..)、深度学习等等领域
* Miniconda 是 Conda 的轻量安装包，只带了 Conda、核心依赖、少量必要工具（pip), 由用户选择性安装依赖
* 如果需要预装大量科学计算依赖，再考虑 Anaconda 安装包

## 安装

1. 来到[官网下载界面](https://www.anaconda.com/download/success?reg=skipped)

    ![01.png](/assets/images/技术教程类/运行环境/Python/conda/安装配置Conda/01.png)

2. 跟着图片进行安装就行

    ![02.png](/assets/images/技术教程类/运行环境/Python/conda/安装配置Conda/02.png)

    ![03.png](/assets/images/技术教程类/运行环境/Python/conda/安装配置Conda/03.png)
    
    ![04-Install.png](/assets/images/技术教程类/运行环境/Python/conda/安装配置Conda/04-Install.png)
    
    ![05.png](/assets/images/技术教程类/运行环境/Python/conda/安装配置Conda/05.png)
    
    ![06.png](/assets/images/技术教程类/运行环境/Python/conda/安装配置Conda/06.png)

    * 选择安装目录

    ![07.png](/assets/images/技术教程类/运行环境/Python/conda/安装配置Conda/07.png)

    * 注意安装目录不能有东西, 否则会警告

    ![08.png](/assets/images/技术教程类/运行环境/Python/conda/安装配置Conda/08.png)

    * 安装选项

    ![09.png](/assets/images/技术教程类/运行环境/Python/conda/安装配置Conda/09.png)

    * 安装选项建议

    ![10.png](/assets/images/技术教程类/运行环境/Python/conda/安装配置Conda/10.png)

    * 安装成功

    ![11-FinishInstall.png](/assets/images/技术教程类/运行环境/Python/conda/安装配置Conda/11-FinishInstall.png)

## 配置

* 配置国内镜像源（清华），解决下载速度问题

    ```shell
    conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
    conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
    conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
    conda config --set show_channel_urls yes
    ```

* 验证镜像源配置，并设置环境路径

    ```shell
    conda config --show channels
    conda config --show show_channel_urls
    
    # 我的输出
    D:\>conda config --show channels
    channels:
      - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
      - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
      - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
      - defaults
    
    D:\>conda config --show show_channel_urls
    show_channel_urls: True
    ```

* 将环境指向我自己的 Runtime-Environment 文件夹( 读者选做 )
* 这样可以把 Conda 的环境目录和包缓存目录迁移到 D 盘
* 注意路径中不要包含 envs 和 pkgs 子文件夹，conda 会自动创建

    ```shell
    conda config --add envs_dirs D:\Users\Programming\Python-Development\Runtime-Environment\envs
    conda config --add pkgs_dirs D:\Users\Programming\Python-Development\Runtime-Environment\pkgs
    ```


* 验证环境路径是否生效

    ```shell
    conda config --show envs_dirs
    conda config --show pkgs_dirs
    
    # 我的输出
    D:\>conda config --show envs_dirs
    envs_dirs:
      - D:\Users\Programming\Python-Development\Runtime-Environment\envs
      - D:\Users\Programming\Python-Development\Environment-Manager\MiniConda\MiniConda3-py314_26.5.3-1\envs
      - C:\Users\春木秋水\.conda\envs
      - C:\Users\春木秋水\AppData\Local\conda\conda\envs
    
    D:\>conda config --show pkgs_dirs
    pkgs_dirs:
      - D:\Users\Programming\Python-Development\Runtime-Environment\pkgs
    ```

* IDE( PyCharm )必须全部关掉, 重新打开
* `Invalidate and Restart` 不行, 没有彻底关掉进程、读不到最新环境变量

    ```shell
    # 查看环境变量路径
    echo %path:;=&echo.%
    
    # IDE Terminal 检验
    conda --version
    ```

* 后续命令操作会有 conda 服务条款确认，直接 accept 确认就行

    ```shell
    (.venv) D:\Users\Programming\Python-Development\Projects\>conda create -n demo python=3.10
    Do you accept the Terms of Service (ToS) for https://repo.anaconda.com/pkgs/main? [(a)ccept/(r)eject/(v)iew]: 
    Do you accept the Terms of Service (ToS) for https://repo.anaconda.com/pkgs/r? [(a)ccept/(r)eject/(v)iew]: 
    ```



