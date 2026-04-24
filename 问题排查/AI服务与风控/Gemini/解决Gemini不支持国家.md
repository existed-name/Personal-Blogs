# 解决Gemini不支持国家

---

## 1、背景
* **配置**: `C lash^Ver ge2.4.4`、`规则模式`、开启了`DNS覆写`
* **问题描述**: 虽然解决了<u>[Gemini Somthing Went Wrong 的问题](/问题排查/AI服务与风控/Gemini/解决Gemini异常：SomethingWentWrong.mdingWentWrong.md)</u>，但用了4个星期，有2次遇到地区限制。第1次的时候，我把它冷处理: 关了，不管它，第2天好了。但昨天又来，实在受不了，就折腾1天解决了，这里记录解决方法



## 2、结论
DNS泄露，它看得到我的国内DNS服务器，于是就认为我不在国外



## 3、解决方法
### (1)修改`NDS覆写`规则
打开软件 → `设置` → `C lash设置` → 进入`DNS覆写`设置 → 进入右上角`高级`
![](/assets/images/问题排查/AI服务与风控/Gemini/解决Gemini不支持国家/01-DnsOverwriting.png "")  
![](/assets/images/问题排查/AI服务与风控/Gemini/解决Gemini不支持国家/02-Advanced.png "")  

然后可以看到一堆yaml格式的代码，用以下代码覆盖掉
```yaml
dns:
  enable: true                  # 开启 Clash 内置 DNS
  listen: ':53'                 # 本地监听 53 端口
  enhanced-mode: fake-ip        # 使用 fake-ip 模式(推荐规则模式)
  fake-ip-range: 198.18.0.1/16  # 假IP分配范围
  fake-ip-filter-mode: blacklist # 黑名单模式(对应的fake-ip-filter列表不走fake-ip)
  
  prefer-h3: false               # 优先用HTTP/3(false关掉更稳)
  respect-rules: true           # DNS查询遵循代理规则(必须开)
  use-hosts: true               # 允许hosts文件生效
  use-system-hosts: false       # 不使用系统hosts
  ipv6: false                   # 禁用IPv6(防泄露)

  fake-ip-filter:               # 以下域名不使用fake-ip
    - '*.lan'
    - '*.local'
    - '*.arpa'
    - 'time.*.com'
    - 'ntp.*.com'
    - '+.market.xiaomi.com'
    - 'localhost.ptlogin2.qq.com'
    - '*.msftncsi.com'
    - 'www.msftconnecttest.com'

  default-nameserver:           # 启动时用于解析DoH地址(地基DNS)
    - '223.5.5.5'
    - '119.29.29.29'

  nameserver:                   # 主DNS服务器(国内DoH)
    - 'https://doh.pub/dns-query'
    - 'https://dns.alidns.com/dns-query'

  direct-nameserver-follow-policy: false

  fallback-filter:              # 触发fallback的条件
    geoip: true
    geoip-code: 'CN'
    ipcidr:
      - '240.0.0.0/4'
    domain:                     # 占位触发域名(找一些国内网站替换即可)
      - '+.zhihu.com'
      - '+.feishu.cn'
      - '+.bilibili.com'
      - '+.kimi.com'
      - '+.deepseek.com'
      - '+.doubao.com'

  fallback:                     # 备用DNS(国外)
    - 'https://dns.google/dns-query'
    - 'https://1.1.1.1/dns-query'

  proxy-server-nameserver:      # 解析代理服务器域名时使用
    - 'https://doh.pub/dns-query'

  direct-nameserver: []

  nameserver-policy:            # 精准分流DNS
    geosite:cn:
      - 'https://doh.pub/dns-query'
      - 'https://dns.alidns.com/dns-query'
    geosite:google,youtube,telegram:
      - 'https://dns.google/dns-query'
      - 'https://1.1.1.1/dns-query'
```

然后保存


### (2)关闭ipv6
1. ipv6也会泄露DNS😓然后软件对ipv6的隐藏可能不太好，所以把`C^lash`有关ipv6解析的设置关掉

2. `Win + I`打开设置 → `网络和Internet`
* **如果用的网线**: `以太网` → `DNS服务器分配`改为自动而不是手动设定(以前没爬楼梯的时候手动指定过`8.8.8.8`、`8.8.4.4`，虽然好像没什么用😂现在才发现这个是给网线用的😅)
* **用Wifi**: `WLAN` → 设置当前WiFi的属性 → 自动分配DNS服务器
![](/assets/images/问题排查/AI服务与风控/Gemini/解决Gemini不支持国家/03-WlanDnsServer.png "")  

3. `网络和Internet` → `高级网络设置` → 当前在用的`网络适配器` → `更多适配器选项`编辑 → 取消勾选`Intern协议版本6(Ipv6)` → 确定
![](/assets/images/问题排查/AI服务与风控/Gemini/解决Gemini不支持国家/04-EditAdapter.png "")  
![](/assets/images/问题排查/AI服务与风控/Gemini/解决Gemini不支持国家/05-DisableIpv6.png "")  


### (3)Edge设置
1. 设置 → 隐私 → 安全 → 关闭`使用安全DNS`(不过默认就是关的，只是确认一下)——否则浏览器会绕过`C^lash`自己解析
![](/assets/images/问题排查/AI服务与风控/Gemini/解决Gemini不支持国家/06-DisableSecureDns.png "")  

2. 地址栏搜索`edge://flags/`，进入实验功能 → 搜索`WebRTC`，启用`enable-webrtc-hide-local-ips-with-mdns`，防止网站通过 WebRTC 获取真实内网`I P`(应该不用专门去下载扩展了吧🤔)
![](/assets/images/问题排查/AI服务与风控/Gemini/解决Gemini不支持国家/07-WebRTC.png "")  

3. 隐私 → 网站权限 → 所有权限 → 位置，改成`询问`(默认就是询问)
![](/assets/images/问题排查/AI服务与风控/Gemini/解决Gemini不支持国家/08-SiteLocation.png "")  

4. 电脑系统设置 → 关闭`允许应用访问位置`、`定位服务`。让网站通过`I^P`定位
![](/assets/images/问题排查/AI服务与风控/Gemini/解决Gemini不支持国家/09-SystemLocation.png "")  



## 4、检验方法
> 可以开无痕浏览或者新的EdgeProfile来测试
1. Google搜索页面位置: 进入<u>https://google.com/</u>，随便搜1个东西，翻到页面底部查看显示的位置信息。如果显示非国内地址那就行了；而且如果泄露了，可能进去的网址是`https://google.com.hk`(DNS服务器是香港的)
![](/assets/images/问题排查/AI服务与风控/Gemini/解决Gemini不支持国家/10-GoogleSearch.png "")  

2. Gmail/Google Account的右上角`google apps`，可以看到YouTube、Gemini
![](/assets/images/问题排查/AI服务与风控/Gemini/解决Gemini不支持国家/11-GoogleApps.png "")  

3. YouTube Premium测试: 访问<u>https://youtube.com/red</u>，如果正常显示YouTube Premium的订阅选项和价格，说明DNS没泄露(或者泄露比较少)；否则会显示国家地区不支持
![](/assets/images/问题排查/AI服务与风控/Gemini/解决Gemini不支持国家/12-YoutubePremium.png "")  

4. 也可以用测试工具: <u>https://whoer.net/zh</u>、<u>https://browserleaks.com/dns</u>、<u>https://www.browserscan.net/zh/</u>等等，但是注意 **`规则模式`下测试一定有泄露，这是正常现象，只要能用Gemini就说明没问题**



## 5、附
1. `全局模式`或者`TUN`应该也可以(然而我`规则模式`够用了，而且也怕费流量😅)，不过主要是改`DNS覆写`防泄露——但是我不理解，为什么以前用默认的`DNS覆写`配置 + 规则模式，还是可以正常用Gemini🤨可能是Google又加强风控了😑

2. `DNS覆写`的代码可以让AI针对自己的情况修改优化。虽然说这个配置可以正常用Gemini，但还是挂了1些`_节^`点，之前改着改着直接全部挂了，然后发现是
```yaml
  proxy-server-nameserver:      # 解析代理服务器域名时使用
    - 'https://doh.pub/dns-query' # 不能用国外服务器比如 https://dns.google/dns-query
```

3. 缺点: 可能不稳定，比如跟AI聊着聊着，然后中断了，需要重新刷新，AI说是杰`点`质量问题🤔可以试一下YouTube观看体验找比较好的那个

4. 排查过程
* 最开始的时候，我以为是Edge的天气预报泄露我的位置，然后把国外位置设为主要位置，再后来直接ban了定位
* 我又找到了<u>https://www.bing.com/account/</u>，设置bing搜索界面为英语、美国(在这个页面最下面保存)，但是发现新开窗口还是原样😓
* 无痕浏览不行
* AI说我Payment有残留的国家地区，然而我进Google设置里面看，我也没绑卡啊
* 而且Google Account也没有设置`home address`、`work address`
* 然后网上一搜，找到了<u>[Gemini地区限制快速诊断](https://yingtu.ai/blog/gemini-region-restriction-diagnosis-guide-2025)</u>这篇文章，发现了通过Google搜索页显示位置、YoutubePremium来检测地区的办法
* 再把观察到的信息喂给AI，然后知道这是DNS泄露——软件配置有一点问题
* 之后继续折腾各种测试网站、`DNS覆写`配置，才慢慢改出来😵
* 途中也发现，从`机房IDC`换成`家庭宽带`I_P，虽然跟原来用的I_P一样脏，但是直接就可以用，甚至不用过多设置`DNS覆写`——也就是说，**根本上还是I_P质量的问题**🤔

---

> **往期文章**  
> <u>[注册Google又被封了😅](/技术教程类/注册Google又被封了.md)</u>  
> <u>[Edge防AI风控配置](/技术教程类/Edge防AI风控配置.md)</u>  
> <u>[解决Gemini异常：SomethingWentWrong](/问题排查/AI服务与风控/Gemini/解决Gemini异常：SomethingWentWrong.mdingWentWrong.md)</u>  
> <u>[Claude注册](/技术教程类/Claude注册.md)</u>  
> ……  
