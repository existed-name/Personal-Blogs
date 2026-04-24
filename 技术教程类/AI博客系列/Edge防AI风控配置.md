# Edge防AI风控配置


---

## 1、描述
平时会用到网页端Gemini、Claude，但是害怕风控😅，于是找AI了解通过设置Edge的配置来降低风险


## 2、原理
AI平台会看用户的安全程度和“干净程度”，安全程度：账号本身的安全设置（比如Google会绑邮箱、电话、验证器等等）、非人类/不正常行为（批量养号、自动脚本、乱跳IP等等）

干净程度：我的IP不够干净，但可以让“设备”更加干净。于是设置浏览器，**专门用1个Profile来登录**Gemini、Claude（每个Profile就可以**模拟一个设备、隔离之前的数据**），保持AI平台检测到的设备指纹固定、比较干净，再辅助一些网络设置降低检测风险。这样就算换了IP也不需要担心


## 3、操作
### （1）创建Profile
点击Edge右上角个人头像 → `创建新的个人资料`，就可以得到新的Profile（在`个人资料`里面可以修改）  
![创建个人配置](/assets/images/技术教程类/AI博客系列/Edge防AI风控配置/01-CreateNewProfile.png "创建个人配置")
![不需要登录账号](/assets/images/技术教程类/AI博客系列/Edge防AI风控配置/02-NoSignIn.png "不需要登录账号")

也可以直接点标签栏的`＋`新建标签页
![一路继续](/assets/images/技术教程类/AI博客系列/Edge防AI风控配置/03-StartBrowsing.png "一路继续")
之后会有一点风格设置，随便设置即可


### （2）设置`隐私、搜索和服务`
![进入浏览器设置](/assets/images/技术教程类/AI博客系列/Edge防AI风控配置/04-PrivacySettings.png "进入浏览器设置")

①跟踪防护 → `启用跟踪防护` & `严格`

②隐私 → `发送“禁止跟踪”请求`

③Cookie 和站点权限 → `允许站点保存和读取 Cookie 数据`、`阻止第三方 Cookie`、关闭`预加载页面以更快浏览和搜索`（减少后台请求）

④清除浏览数据 → `选择每次关闭浏览器时要清除的内容` → 打开`Cookie 和其他站点数据`，并设置`不要清除`<u>https://gemini.google.com/</u>、<u>https://claude.ai/</u>等Google/Claude网站（这样可以保持登录状态，避免频繁登录验证；后续可以隔几个星期手动清除）；然后浏览历史清不清都差不多（清了看起来更舒服）


### （3）防WebRTC泄漏
AI推荐安装拓展，但是我嫌麻烦😂就用的Edge自带的设置：地址栏搜索<u>edge://flags/</u>进入实验功能 → 搜索`enable-webrtc-hide-local-ips-with-mdns` → `enabled`，它可以把内网IP用一串乱码（mDNS 主机名）替换掉  
![启用浏览器实验功能](/assets/images/技术教程类/AI博客系列/Edge防AI风控配置/05-HideIP.png "启用浏览器实验功能")


### （4）额外设置（可选）
因为用的美国IP，这里还有一些细节可能要改  
①设置 → 语言 → 用`英语(美国)`展示（不需要删除非首选语言；注意Edge改语言会应用到整个浏览器而不是单个Profile）  
②电脑系统时区改成IP所在地的时区（这个确实😶……


## 4、备注
（1）新的Profile用普通窗口就行，无痕浏览不会保留Cookie、关了后需要重新登录；然后各个Profile之间没有关系、收藏夹之类的需要新建

（2）频繁修改浏览器设置、切换IP，建议先把Gemini/Claude的窗口关掉、修改好后再打开

（3）指纹遮蔽的梯度大概是：专业指纹浏览器（AdsPower） > 轻量级指纹浏览器（Dolphin Anty） > Chrome ≈ Edge > FireFox，不过应该还没有到用指纹浏览器的地步，而且Edge/Chrome用的人多、看起来更真实

（4）具体的原理、更多设置可以问AI  
![原理说明](/assets/images/技术教程类/AI博客系列/Edge防AI风控配置/06-Explanation.png "原理说明")

---

> **往期文章**  
> <u>[解决Gemini“Something went wrong”](/问题排查/AI服务与风控/Gemini/解决Gemini异常：SomethingWentWrong.md)</u>  
> <u>[Claude网页端注册](/技术教程类/Claude注册.md)</u>  
