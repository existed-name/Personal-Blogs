# ClaudeCode安装 + CC-Switch配置

<details>
    <summary>目录</summary>

1. [背景](#1背景)
2. [前期准备](#2前期准备)
   1. [安装配置 Git](#1安装配置-git)
   2. [安装配置 NVM](#2安装配置-nvm)
3. [配置 Claude Code](#3配置-claude-code)
   1. [安装](#1安装)
   2. [安装配置 CC-Switch](#2安装配置-cc-switch)
   3. [开始体验](#3开始体验)
4. [其他注意事项](#4其他注意事项)
   1. [安装时报了一堆 Error](#1安装时报了一堆-error)
   2. [安装时有 Warn](#2安装时有-warn)
   3. [彻底卸载 ClaudeCode](#3彻底卸载-claudecode)
   4. [切换 Node 版本后用不了](#4切换-node-版本后用不了)
  
</details>

---

---

# 1、背景
* 看大家都在讨论 Agent 智能体、CLI 命令行界面，也想试试，就跟着 AI 的教程 + 同学指导弄了 ClaudeCode

* 这里是配置清单
    - NVM(`Node Version Manager`): 集中管理 Node、切换 Node 版本, 就不用单独下载 Node 了
    - Git(可选): Claude Code 用来提交代码
    - Windows PowerShell: 用 cmd 可能不太兼容(比如图形化显示)
    - 官网订阅模型的 API Key


# 2、前期准备
## (1)安装配置 Git

* 可参考这篇博客: <u>[安装GitForWindow](/技术教程类/开发工具&插件/Git/安装GitForWindow.md)</u>

* 如果只是简单体验，也可以不要 Git，或者以后补上

    ```markdown
    # AI 的解释: ClaudeCode 用 Git 提供更好的终端执行环境
    * 不装 Git：ClaudeCode 能正常跑，但内部 shell 工具会 fallback 到 PowerShell。功能受限（尤其是执行复杂命令、文件操作、项目管理时体验差）。
    
    * 装 Git for Windows：ClaudeCode 会优先用 Git Bash（更好的 Unix-like 环境），命令执行更稳定、功能更全（推荐）
    ```


## (2)安装配置 NVM

1. 可参考这篇博客: <u>[Node.js 版本管理](/技术教程类/运行环境/Node.js/Node.js切换版本.md)</u>

2. 记得切换镜像源，加速国内网络下载

    ```shell
       nvm node_mirror https://npmmirror.com/mirrors/node/
       nvm npm_mirror https://npmmirror.com/mirrors/npm/
    ```

3. 配置好后命令行 `nvm install 22`、`nvm use 22` 使用 Node22( Claude Code 最低 Node18，Node24 可能有点新、避免不兼容 )

4. 注意检查 npm 全局路径

    ```shell
    # 查看当前的 npm 全局路径
    npm config get prefix
    
    # 确保 npm 全局路径 = 系统的 `NVM_SYMLINK` 变量路径
    # 比如通过电脑设置 → 系统 → 系统信息 → 高级系统设置查看环境变量
    # 或者 PowerShell 查看环境变量
    Get-ChildItem Env: | Out-String
    # $env:path -split ";"
    ```
   
5. 如果 npm 全局路径 != 系统的 `NVM_SYMLINK` 变量路径
   
    * 这种情况会导致之后安装 Claude Code 报错
   
    ![](/assets/images/技术教程类/AI博客系列/ClaudeCode安装-CCSwitch配置/01.png "")

    * 比如之前删了 Node，有残留 npm 路径

    * 修改操作
   
    ```shell
    # 强制删除 npm 配置文件中残留的旧 prefix 路径
    npm config delete prefix --global
    npm config delete prefix
    
    # 重新激活 Node 22，让 NVM 重新把全局路径指向 NVM_SYMLINK
    nvm use 22
    
    # 再次查看当前的全局路径
    npm config get prefix
   
    # 还是不行，就强制将全局 prefix 覆盖写入用户配置文件，指向 NVM 的标准软链接
    npm config set prefix "自己的 NVM_SYMLINK 路径" --global
    ```



# 3、配置 Claude Code
## (1)安装

1. 打开 PowerShell
    * 右键屏幕 → 终端
    * 或者 win + R 输入 cmd 打开 cmd 窗口，然后新建窗口就是 PowerShell
    * 目录 `"C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe"`

2. 安全起见可以切换到 D 盘某个目录运行命令(比如 `cd D:\Agent\Claude-Code`)，避免出现意外情况、在命令行所在的 C 盘安装到东西

3. 开始安装
    
    ```shell
    # 正式开始全局安装 Claude Code 官方 CLI 工具
    npm install -g @anthropic-ai/claude-code
   
    # 无异常出现，只输出 added 2 packages in 2s
    ```

4. 验证安装

   ```shell
   # 查找可执行文件路径
   where claude
   
   # 查看版本
   claude --version
   
   # 让Claude自检运行环境，确保Git权限、网络链路全部畅通
   claude doctor
   ```   

   ![](/assets/images/技术教程类/AI博客系列/ClaudeCode安装-CCSwitch配置/02.png "")

5. 输入 `claude` 启动

   ![](/assets/images/技术教程类/AI博客系列/ClaudeCode安装-CCSwitch配置/03.png "")

   * 可以看到不能访问官网模型，于是接下来就是通过 CC-Switch 切换到其他模型


    
## (2)安装配置 CC-Switch

1. 来到<u>[GitHub 官网 Release 界面](https://github.com/farion1231/cc-switch/releases)</u>

2. 下载 `Assets` 里面的 `CC-Switch-v3.15.0-Windows.msi`( 可能需要点击左下角 `Show all 18 assets` 展开所有内容 )

   ![](/assets/images/技术教程类/AI博客系列/ClaudeCode安装-CCSwitch配置/04-DownloadCcSwitch.png "")

3. 注意下载的时候 Edge 可能警告不安全, `Report this file as safe` → 简单填写一点 → 然后 `Keep` 就可以完成下载

   ![](/assets/images/技术教程类/AI博客系列/ClaudeCode安装-CCSwitch配置/05.png "")

4. 然后启动这个 `.msi` 安装器, 只需要修改安装目录，其他直接 `next` 就行

   ![](/assets/images/技术教程类/AI博客系列/ClaudeCode安装-CCSwitch配置/06.png "")

   ![](/assets/images/技术教程类/AI博客系列/ClaudeCode安装-CCSwitch配置/07.png "")

   ![](/assets/images/技术教程类/AI博客系列/ClaudeCode安装-CCSwitch配置/08.png "")

5. 进去过后点击右上角 `➕`，添加供应商，自定义配置
   * 填写相关信息
   * 可以让 AI 生成一个详细的 JSON 配置模板，改成自己的数据，再填进去

   ![](/assets/images/技术教程类/AI博客系列/ClaudeCode安装-CCSwitch配置/09.png "")

6. 保存后回到主页面，切换到自己的模型


## (3)开始体验

* 再 `claude` 就可以体验了

  ![](/assets/images/技术教程类/AI博客系列/ClaudeCode安装-CCSwitch配置/10.png "")



# 4、其他注意事项

* **把命令行复制给 AI，让 AI 帮忙**

## (1)安装时报了一堆 Error

* 本人遇到的情况是 npm 路径残留
* 也就是目录的: `2、前期准备` - `(2)安装配置 NVM` - `5. npm 全局路径 != 系统的 NVM_SYMLINK 变量路径`


## (2)安装时有 Warn

* 我的情况是已有 ClaudeCode 的情况下再次安装 ClaudeCode + npm 路径残留(也就是上一个注意事项的内容)

  ![](/assets/images/技术教程类/AI博客系列/ClaudeCode安装-CCSwitch配置/11.png "")

* 删掉 `claude` 文件、`claude.cmd` 和 `claude.ps1` 脚本
* 删掉 `node_modules` 里面的 `@anthropic-ai` 文件夹
* 修改 npm 路径
* 卸载之前已有的 ClaudeCode 再重装


## (3)彻底卸载 ClaudeCode

* (应该不需要吧)
* 除了删除`claude`、`claude.cmd`、`claude.ps1`、`@anthropic-ai`
* 还要删掉 `C:\Users\用户名` 的 `.claude.json` 文件、`.claude` 文件夹
* 再 `Get-ChildItem Env: | Out-String` 查看有没有 Claude 的环境变量
* 以及检查注册表

   ```shell
   # 1. 检查并强制删除【当前用户注册表】中可能存在的 Anthropic / Claude 痕迹
   Get-ChildItem HKCU:\Software | Where-Object { $_.Name -like "*anthropic*" -or $_.Name -like "*claude*" } | Remove-Item -Force -Recurse
   
   # 2. 检查并强制删除【系统全局注册表】中可能存在的 Anthropic / Claude 痕迹
   Get-ChildItem HKLM:\Software | Where-Object { $_.Name -like "*anthropic*" -or $_.Name -like "*claude*" } | Remove-Item -Force -Recurse
   ```


## (4)切换 Node 版本后用不了

* 在 Node22 安装 ClaudeCode，然后切换 Node 版本(比较老旧的 vue 项目需要)，发现命令行(cmd,powershell)不能识别 `claude`
* 切换回 Node22 后

    ```shell
    # 正常输出
    where claude
    
    # 然而
    claude
    claude --version
    # 报错
    '"xxx\\node_modules\@anthropic-ai\claude-code\bin\claude.exe"' 不是内部或外部命令，也不是可运行的程序或批处理文件。
    # 注意这里还是双斜杠，系统定位路径出错
    ```
  
* AI 推荐的最直接有效的操作是每次切换 Node 版本后，**删残留 + 重新全局安装** 

    ```shell
    # 卸载
    npm uninstall -g @anthropic-ai/claude-code
    
    # 查看 npm 全局路径
    npm config get prefix
    
    # 删除残留文件
    del npm 全局路径\claude*
    rd /s /q "npm 全局路径\node_modules\@anthropic-ai"
    
    # 重新安装
    npm install -g @anthropic-ai/claude-code
    
    # 验证
    claude --version
    where claude
    ```

* 我在想有没有其他的方法，AI 的解释 ↓ (当成科普看看就行)

    > 因为 NVM 每个版本的全局目录是隔离的  
    > 
    > 切换版本时，旧版本的 shim 文件仍然留在磁盘上，而且 Windows 的 where 和 PATH 可能优先找到残留的旧 shim  
    > 
    > 旧 shim 内部记录的路径可能带双反斜杠或指向已失效的 node_modules，导致即使切回原版本也执行失败   
    > 
    > NVM 的 shim 机制在 Windows 上不够健壮，残留文件不会自动清理
 
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
