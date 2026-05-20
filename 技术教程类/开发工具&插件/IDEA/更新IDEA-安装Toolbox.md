# 更新IDEA+安装Toolbox

<details>
<summary>目录</summary>

- [(一)背景描述](#一背景描述)
- [(二)配置 Toolbox](#二配置-toolbox)
  - [1、安装](#1安装)
  - [2、设置](#2设置)
- [(三)更新 IDEA](#三更新-idea)
  - [1、Toolbox 安装新版 IDEA](#1-toolbox-安装新版-idea)
  - [2、检查新版 IDEA](#2-检查新版-idea)
  - [3、删除旧版 IDEA](#3-删除旧版-idea)
- [(四)附带: 更新 DataGrip](#四-附带-更新-datagrip)

</details>

---

---

# (一)背景描述
* 听说 IDEA 新版对 AI 插件/命令行等等支持更好，于是想更新到最新版看看(我是 2025.1)
* 因为用 Toolbox 更方便管理 JetBrains 家的 IDE，所以这里选择安装 Toolbox 来更新 IDEA
* 这里**记录安装配置 Toolbox、更新 IDEA、卸载旧版、附带的更新 DataGrip**



# (二)配置 Toolbox
## 1、安装
* 来到 <u>[Toolbox 官网](https://www.jetbrains.com.cn/toolbox-app/)</u>, 下载 `.exe` 安装器

* 运行安装器，这玩意儿只能安在 C 盘(`C:\Users\用户名\AppData\Local\JetBrains\Toolbox\bin\jetbrains-toolbox.exe`)比较恼火

   ![](/assets/images/技术教程类/开发工具&插件/IDEA/更新IDEA-安装Toolbox/01-InstallToolbox.png "")

   ![](/assets/images/技术教程类/开发工具&插件/IDEA/更新IDEA-安装Toolbox/02.png "")

* 没有桌面快捷方式，需要自己手动创建

  ![](/assets/images/技术教程类/开发工具&插件/IDEA/更新IDEA-安装Toolbox/03.png "")


## 2、设置
* 进入 Toolbox 的设置界面，如下是我的设置，可根据个人情况调整

1. 外观与行为
    * 关闭开机自启动
    * 关闭自动更新 Toolbox

   ![](/assets/images/技术教程类/开发工具&插件/IDEA/更新IDEA-安装Toolbox/04.png "")

2. 工具
    * 关闭 `自动更新所有工具`
    * 设置 `工具安装位置`: 我是 `D:\Users\Programming\IDE`

   ![](/assets/images/技术教程类/开发工具&插件/IDEA/更新IDEA-安装Toolbox/05.png "")

    * 设置 `Shell 脚本位置`: 我是 `D:\Users\Programming\IDE\Tool-Box\scripts`(默认 `C:\Users\用户名\AppData\LocalJetBrains\Toolbox|scripts`)
    * 然后把 `Shell 脚本位置目录`添加到 `Path` 环境变量里面(设置 → 系统 → 系统信息 → 高级系统设置)

   ![](/assets/images/技术教程类/开发工具&插件/IDEA/更新IDEA-安装Toolbox/06.png "")


# (三)更新 IDEA
## 1、Toolbox 安装新版 IDEA
* 因为我以前是下载的 IDEA 安装器、也没添加 IDEA 的环境变量，所以 Toolbox 没有识别到 IDEA，就没显示 `更新`
* 直接从 Toolbox 安装 IDEA2026.1 到自己设置的`工具安装位置`
* 然后 Toolbox `工具`界面 → `已安装` → IDEA 右边 3 个点进入设置 → 关闭自动更新


## 2、检查新版 IDEA
1. 从旧的 IDEA → `Manage IDEA Settings` → `Export Settings` 导出原来的配置

2. 看看新的 IDEA 个人设置有没有什么变化
    * 代码展示行数、代码大小
    * IDEA 外观
    * 自定义的代码模板(Settings → Editor → File and code template，或者直接手动创建文件看看)
    * 等等
    * 不对的话就导入刚刚的配置

3. 另外发现它给插件升了级，并且多了点自带的插件
    * `MCP Server`：2025.2+ 内置，默认启用，支持外部AI客户端（如Cursor、Claude Desktop、Codex）通过Model Context Protocol调用IDEA工具，实现跨工具Agent控制
    * `Node.js Remote Interpreter`：增强了远程主机、WSL 等环境下的 Node.js 解释器配置和调试支持。
    * `Spring`：2026.1 重点增强，提供 Spring Runtime Insight（运行时 Bean、端点、属性实时查看）等高级集成能力。
    * `Full Line Code Completion`：默认捆绑的本地 ML 多行/整行代码补全插件（Ultimate 可用），无需联网即可提供完整代码建议。
   
    ![](/assets/images/技术教程类/开发工具&插件/IDEA/更新IDEA-安装Toolbox/07.png "")

4. 有一说一启动界面更好看了

   ![](/assets/images/技术教程类/开发工具&插件/IDEA/更新IDEA-安装Toolbox/08.png "")

   ![](/assets/images/技术教程类/开发工具&插件/IDEA/更新IDEA-安装Toolbox/09.png "")



## 3、删除旧版 IDEA
1. 用一段时间新版感觉没问题后，就可以删除旧版

2. 先从电脑设置 → 应用 → 安装的应用 → 看看可不可以删除旧版 IDEA

    ![](/assets/images/技术教程类/开发工具&插件/IDEA/更新IDEA-安装Toolbox/10.png "")
    
    ![](/assets/images/技术教程类/开发工具&插件/IDEA/更新IDEA-安装Toolbox/11.png "")

3. 从电脑设置删不掉，那就直接来到 `Uninstall.exe` 所在位置、启动它

    ![](/assets/images/技术教程类/开发工具&插件/IDEA/更新IDEA-安装Toolbox/12.png "")

    * 旧版配置、数据可见 ↓
   ```text
    C:\Users\用户名\AppData\Roaming\JetBrains
    ├── consentOptions
    ├── DataGrip2025.3
    ├── DataGrip2026.1
    ├── Idea
    ├── IntelliJIdea2025.1
    ├── IntelliJIdea2026.1
    ├── bl
    ├── crl
    ├── PermanentDeviceId
    └── PermanentUserId

     C:\Users\用户名\AppData\Local\JetBrains
     ├── Daemon
     ├── DataGrip2025.3
     ├── DataGrip2026.1
     ├── IntelliJIdea2025.1
     ├── IntelliJIdea2026.1
     └── Toolbox
    ```

4. 此时就只剩新版 IDEA 了，创建快捷方式即可



# (四)附带: 更新 DataGrip
1. 我的 DataGrip 以前安装时自动设置了用户环境变量，可能因为这个 Toolbox 找到了它，在它的目录原地更新
    * 于是表面上他还是原来的版本(比如电脑设置 → 已安装的应用里面)，但实际已经是新版(DataGrip → 顶部菜单栏 → Help → About)
    * 这个时候就需要删除、重新安装，避免管理混乱

2. 先导出 settings，然后把 DataGrip 的环境变量删掉(电脑设置 → 系统 → 系统信息 → 高级系统设置)

3. 从电脑设置 → 应用 → 安装的应用 → 看看可不可以删除 DataGrip
    * 不能删除的话就用它的 `Uninstall.exe` 进行删除

4. 我发现**用 Toolbox 删 IDE 删不干净**
    * C 盘的 AppData 还在
    * 而且由于 C 盘权限，需要管理员运行 cmd `takeown /F "C:\Users\用户名\AppData\Roaming\JetBrains\DataGrip2025.3" /R /D Y` 拿到权限，再来删除
    * `安装的应用`还有 DataGrip(注册表残留): Win + R → regedit → `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall` 手动删除 DataGrip 旧版注册表

5. 然后给新版创建快捷方式即可

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