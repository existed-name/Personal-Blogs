# 安装GitForWindow

---

---


1. 来到 <u>[Git 官网下载界面](https://git-scm.com/install/windows)</u>，点击 `Git for Windows/x64 Setup` 下载安装器

    ![](/assets/images/技术教程类/开发工具&插件/安装GitForWindows/01.png "")

2. 点击运行安装器

    ![](/assets/images/技术教程类/开发工具&插件/安装GitForWindows/02.png "")

3. 选择安装目录

    ![](/assets/images/技术教程类/开发工具&插件/安装GitForWindows/03.png "")

4. 安装选项(如图即可：在默认选项基础上勾选了 `Add a Git Bash Profile to Windows Terminal`)

    ![](/assets/images/技术教程类/开发工具&插件/安装GitForWindows/04.png "")

    - **Additional icons**：在桌面创建 Git 快捷方式（这个不需要，因为都用的命令行）

    - **Windows Explorer integration**：在文件夹右键菜单增加 “Git Bash here” 和 “Git GUI here”
    
    - **Git LFS**：支持 Git Large File Storage，大文件（如图片、视频）不占用仓库空间

    - **Associate .git* files**：让 .gitconfig 等文件默认用文本编辑器打开

    - **Associate .sh files**：.sh 脚本用 Git Bash 运行

    - **Check daily for updates**：每天自动检查 Git 更新（建议关）

    - **Add a Git Bash Profile to Windows Terminal**：在 Windows Terminal（新版终端，需要单独下载）里添加一个 Git Bash 的快捷配置文件，以后用 Windows Terminal 时可以方便切换到 Git Bash

    - **Scalar**：Microsoft 开发的 Git 优化工具，适合超大仓库加速（普通项目保留也没坏处）

5. 设置「开始菜单快捷方式的存放位置」

    * 保持默认即可

    * 安装程序会在开始菜单里创建一个叫「Git」的文件夹，把所有 Git 的快捷方式都放进去

    ![](/assets/images/技术教程类/开发工具&插件/安装GitForWindows/05.png "")

6. 选择 Git 默认使用的文本编辑器(默认 vim，但可能不好用)

    * 如果自己有 VS/VScode 就选自己的 IDE 作为文本编辑器
    
    * 这里选的 Notepad，也就是系统的记事本

    * 之后还可以命令行设置

    ![](/assets/images/技术教程类/开发工具&插件/安装GitForWindows/06.png "")

7. 初始分支命名

    * 选中 `Override the default branch name for new repositories`（第二个选项）

    * 然后在下面的输入框里输入 `main`

        ![](/assets/images/技术教程类/开发工具&插件/安装GitForWindows/07.png "")

8. 环境路径(默认选项即可)

            ![](/assets/images/技术教程类/开发工具&插件/安装GitForWindows/08.png "")

9. 选择 SSH 软件(默认即可)

        ![](/assets/images/技术教程类/开发工具&插件/安装GitForWindows/09.png "")

10. HTTPS 传输连接(默认选项即可)

        ![](/assets/images/技术教程类/开发工具&插件/安装GitForWindows/10.png "")

11. 不同系统转换换行(默认选项即可)

        ![](/assets/images/技术教程类/开发工具&插件/安装GitForWindows/11.png "")

12. 终端模拟器(默认选项即可)

        ![](/assets/images/技术教程类/开发工具&插件/安装GitForWindows/12.png "")

13. 设置拉取代码的操作(默认选项即可)

        ![](/assets/images/技术教程类/开发工具&插件/安装GitForWindows/13.png "")

14. 设置凭证管理工具(默认选项即可)

        ![](/assets/images/技术教程类/开发工具&插件/安装GitForWindows/14.png "")

15. 额外选项(默认选项即可)

        ![](/assets/images/技术教程类/开发工具&插件/安装GitForWindows/15.png "")

16. 完成安装

        ![](/assets/images/技术教程类/开发工具&插件/安装GitForWindows/01.png "")

17. 验证安装: 命令行输入 `git --version`

18. 设置用户名、邮箱

    * 每次 commit 到自己 GitHub 账号上面

    ```shell
    git config --global user.name "GitHub 账号名"
    git config --global user.email "GitHub 的隐私邮箱"

    # 查看所有配置(有没有刚才的用户名、邮箱)
    git config list
    ```

    * 可在 <u>https://github.com/settings/emails</u> 的 `Keep my email addresses private` 查看隐私邮箱(`@users.noreply.github.com`)

19. 正在更新后续文章: IDEA 可视化操作 Git、IDEA AI Assistant……

---

> **往期推荐**   
> <u>[解决Gemini不支持国家](/问题排查/AI服务与风控/Gemini/解决Gemini不支持国家.md)</u>  
> <u>[软工.大二下.4月生活日记](/杂谈随笔&个人写作/日记随笔感想/大二下/软工.大二下.4月生活日记.md)</u>  
> <u>[Linux部署SpringBoot项目](/技术教程类/项目部署/Linux部署SpringBoot项目.md)</u>  
> <u>[SpringBoot项目部署-从裸机部署到Docker容器化](/技术教程类/项目部署/SpringBoot项目部署-从裸机部署到Docker容器化.md)</u>  
> ……

---

* 知乎链接整理
> **往期推荐**  
> <u>[个人博客仓库](https://github.com/existed-name/Personal-Blogs/tree/main)</u>  
> <u>[解决Gemini不支持国家](https://www.zhihu.com/question/1936843079798749071/answer/2007801005392273588)</u>  
> <u>[软工.大二下.4月生活日记](https://zhuanlan.zhihu.com/p/2034919650602042004)</u>  
> <u>[Linux部署SpringBoot项目](https://www.zhihu.com/question/589767025/answer/2031853371242583455)</u>  
> <u>[SpringBoot项目部署-从裸机部署到Docker容器化](https://www.zhihu.com/question/635133127/answer/2035476542923478293)</u>  
> ……

