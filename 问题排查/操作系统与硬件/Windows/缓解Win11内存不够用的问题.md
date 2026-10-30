# 缓解Win11内存不够用的问题

---

---

## 背景

* 首先说明，这里的内存是 RAM（Random Access Memory， 随机存取存储器）类似手机的运行内存，不是磁盘/硬盘的存储空间
* 个人配置是 win11, 32G, 16核24线程（可以在任务管理器的性能界面-CPU、内存界面看到信息）
* 前后端项目 + wsl 和 Docker 七八个容器 + 五六个 IDE 窗口 + 一堆杂七杂八的程序，跑大量测试的时候内存容易崩掉，出现 IDE 自动关闭（被系统回收）的现象
* 这里总结了一些缓解方法

---

## 提高虚拟内存

* 内存提交上限 = 内存容量 + 虚拟内存分页文件大小
* win + r 再输入 `sysdm.cpl`
* 进入系统属性设置 → 高级 → 在 `性能` 那一栏点击设置
* 来到性能选项界面 → 在 `虚拟内存` 那一栏点击更改
* 取消勾选 `自动管理所有驱动器的分页文件大小` 
* 设置 C 盘 → 自定义大小 → 初始大小 512，最大值 512，这样固定 512 MB，几乎不占用 C 盘，同时不设为 `无分页文件` 可以让系统崩了的时候正常生成日志到 C 盘、不影响系统允许 → 点击 `设置`
* 设置 D 盘 → 自定义大小 → 我这里设的 32768（32G）-65536（64G） → 点击 `设置`

    ![01-adjust-pagefile-size.png](../../../assets/images/%E9%97%AE%E9%A2%98%E6%8E%92%E6%9F%A5/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%E4%B8%8E%E7%A1%AC%E4%BB%B6/Windows/%E7%BC%93%E8%A7%A3Win11%E5%86%85%E5%AD%98%E4%B8%8D%E5%A4%9F%E7%94%A8%E7%9A%84%E9%97%AE%E9%A2%98/01-adjust-pagefile-size.png)

* **最重要的一步**是修改注册表，让 D 盘的分页文件能够生效
* win + r 再输入 `regedit` → 在上面的地址栏输入 `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management` 按 Enter 来到这个文件夹
* 然后看到右边显示的配置，点击修改里面的 `PagefileOnOsVolume`, 从 1 改成 0

    ![02-regedit-pagefilesonasvolume.png](../../../assets/images/%E9%97%AE%E9%A2%98%E6%8E%92%E6%9F%A5/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%E4%B8%8E%E7%A1%AC%E4%BB%B6/Windows/%E7%BC%93%E8%A7%A3Win11%E5%86%85%E5%AD%98%E4%B8%8D%E5%A4%9F%E7%94%A8%E7%9A%84%E9%97%AE%E9%A2%98/02-regedit-pagefilesonasvolume.png)

* ，我上个月试了好几次没成功、始终只能用 C 盘的分页文件、用不了 D 盘，就是没改这里
* 然后重启电脑，ctrl + shift + esc 打开任务管理器 → 性能界面 → 看到 内存 界面 → 提交上限应该会有明显提升（比如我这里 32G 物理内存 + 32G初始的虚拟内存）

    ![03-restart-and-check-memory.png](../../../assets/images/%E9%97%AE%E9%A2%98%E6%8E%92%E6%9F%A5/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%E4%B8%8E%E7%A1%AC%E4%BB%B6/Windows/%E7%BC%93%E8%A7%A3Win11%E5%86%85%E5%AD%98%E4%B8%8D%E5%A4%9F%E7%94%A8%E7%9A%84%E9%97%AE%E9%A2%98/03-restart-and-check-memory.png)

* 并且在文件资源管理器 → 点击头上的 3 个点 → 点击下拉列表下面的 `选项` 

    ![04-file-resource-manager.png](../../../assets/images/%E9%97%AE%E9%A2%98%E6%8E%92%E6%9F%A5/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%E4%B8%8E%E7%A1%AC%E4%BB%B6/Windows/%E7%BC%93%E8%A7%A3Win11%E5%86%85%E5%AD%98%E4%B8%8D%E5%A4%9F%E7%94%A8%E7%9A%84%E9%97%AE%E9%A2%98/04-file-resource-manager.png)

* 来到 `文件夹选项` 界面 → `查看` → 取消勾选隐藏文件、隐藏受保护的系统文件

    ![05-cancel-hiding-files.png](../../../assets/images/%E9%97%AE%E9%A2%98%E6%8E%92%E6%9F%A5/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%E4%B8%8E%E7%A1%AC%E4%BB%B6/Windows/%E7%BC%93%E8%A7%A3Win11%E5%86%85%E5%AD%98%E4%B8%8D%E5%A4%9F%E7%94%A8%E7%9A%84%E9%97%AE%E9%A2%98/05-cancel-hiding-files.png)

* 然后就可以看到 C 盘下有一个 512MB 的 pagefile.sys, D 盘有一个 32G 的 pagefile.sys（之后记得重新隐藏这些文件，避免误删）, 再去跑一跑吃内存的操作，就成功了
* 当然实际使用的时候，它会先填满物理内存，再去填 C 盘的虚拟内存，再去填 D 盘
* 然后对于注册表同样文件夹目录的 `PageFiles` 参数，它大概显示的是这个内容

    ```text
    c:\pagefile.sys 512 512
    d:\pagefile.sys 32768 65536
    ```
  
* 我试着把 d 盘那一行内容移到 c 盘上面，想让 d 盘优先级更高、先用 D 盘的虚拟内存，但实际上重启就复原了，还是系统盘优先级更高
* 之前我给 C 盘设置无分页文件，它会有如图的警告，问 AI 说是系统出问题的时候会写入 C 盘、取消分页文件后可能写不了, 所以就改成了自定义大小

    ![06-warning-diable-pagefile.png](../../../assets/images/%E9%97%AE%E9%A2%98%E6%8E%92%E6%9F%A5/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%E4%B8%8E%E7%A1%AC%E4%BB%B6/Windows/%E7%BC%93%E8%A7%A3Win11%E5%86%85%E5%AD%98%E4%B8%8D%E5%A4%9F%E7%94%A8%E7%9A%84%E9%97%AE%E9%A2%98/06-warning-diable-pagefile.png)

* 以前室友也试过调整虚拟内存，但打游戏还是比较卡，索性去装内存条了（物理上的根本解决方法... 话说现在内存条好贵, 早先买电脑该直接买 64G，拆下来高价卖出去哈哈

---

## 限制 wsl 内存

* wsl2 的全局配置通常在 `C:\Users\用户名\.wslconfig`
* 如果已经有了配置可以用记事本打开、修改
* 如果还没有配置（装 wsl2 的时候不会自动生成配置），可以按照下面的操作配置
* 注意可以把自己的内存配置、内存问题发给 AI 进行对应的调整
* win + x → 终端 → 打开 powershell，输入以下命令

    ```shell
    # 进入当前 Windows 用户的主目录（通常是 C:\Users\用户名）
    cd ~
    
    # 创建 .wslconfig 文件并写入基础配置；如果文件已存在则直接覆盖
    @"
    [wsl2]
    # 限制WSL2最大物理内存为12GB，根据个人内存大小、使用强度调整
    memory=12GB
    
    # 虚拟处理器数量保持4~8核就行，根据个人内核数量调整
    processors=8
    
    # 交换文件，类似主机的虚拟内存，默认位于系统盘，过大可能会加剧卡顿而非提升性能
    swap=4GB
    
    # 启用稀疏虚拟磁盘，自动压缩WSL2虚拟硬盘，回收未使用空间（可选，但推荐）
    sparseVhd=true
    
    [experimental]
    # 启用自动内存回收：空闲5分钟后逐步回收Linux文件缓存，把内存归还给Windows
    # 这里选的 gradual 逐步温和回收
    # dropcache 会一次性回收，避免太激进、干扰WSL内的Docker守护进程
    autoMemoryReclaim=gradual
    "@ | Out-File -FilePath .wslconfig -Encoding utf8
    ```
  
---

## 调整 IDE 堆栈上限

* PyCharm, IDEA 上面的菜单栏 → 点击后看到右边的 help → 下拉列表下面点击 `Change Memory Settings`
* 把占用的堆大小上限( `Maximum Heap Size` )调整到 3072 ~ 4096MB（根据体验感调整
* 之前设的太高，导致它们占用太多内存、但实际用不了这么多

---

## 调整 docker 容器内存分配

* 调整内存上限、内存交换上限、同一组/网络的容器的总内存上限等等参数
* 这里可以让 ai 根据容器实际的内存使用情况、电脑的内存问题，跑一跑命令，主要给压力大的容器放宽松、其他容器就压缩挤一挤




