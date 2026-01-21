# 解决Gemini异常：`Something Went Wrong`

> <u>[个人博客仓库]()</u>
---

## 1、背景
（1）2个Google号：老号（2024/9/7创建，1年多了）、小号（2025/12/25创建，不到4个星期） 

（2）都是创建不久就被封，然后申诉成功，绑定手机号、恢复邮箱，养号（偶尔看看Gplay、Google、Chrome，以及Gmail收发邮件）

（3）老号登录Claude，小号登录Grok，都可以正常使用，Gmail没问题

（4）然而3个星期以来，Gemini一登录过后就是`Something went wrong`/`出了点问题，请稍后再试`，只能游客模式。Edge无痕浏览/普通窗口、隔一段时间就清除所有浏览数据，依然不行   
![人都麻了](/assets/images/问题排查/解决Gemini异常：SomethingWentWrong/1-SomethingWentWrong.png "人都麻了")



## 3、结论
本人的原因是：**恢复邮箱绑定了但没验证**、**没打开2步验证**



## 2、解决过程
（1）问AI、网上搜，也没找到满意的解决方法。今天又搜了一下（可能是之前搜出来的文章不够新😅），看了<u>[Something went wrong解决方法](https://zhuanlan.zhihu.com/p/1995194901005104334)</u>这篇文章后，突然有个想法：会不会是我的**谷歌账号没设置全**？（后来证明确实如此）


（2）于是登录<u>[Google Account](https://myaccount.google.com/)</u>，检查设置


（3）在`Security & sign-in`中，发现`Recovery emil`竟然没有验证（我记得之前绑定的时候验证过），于是验证邮箱  
![安全设置](/assets/images/问题排查/解决Gemini异常：SomethingWentWrong/2-Security&Sign-in.png "安全设置")  
![验证恢复邮箱](/assets/images/问题排查/解决Gemini异常：SomethingWentWrong/3-VerifyRecoveryEmail.png "验证恢复邮箱")


（4）然后设置`2-Step Verification`  
①点击`Authenticator`  
![添加验证器](/assets/images/问题排查/解决Gemini异常：SomethingWentWrong/4-TwoStepVerification.png "添加验证器")  

②这里需要在手机下载验证器app，作为双因素验证（`2-Factor Authentication`）。`Microsoft Authenticator`/`Google Authenticator`都可以，前者手机应用商店就可以找到（可能就叫`Authenticator`），后者需要上网 + Gplay商店  
![下载Authenticator](/assets/images/问题排查/解决Gemini异常：SomethingWentWrong/5-DownloadAuthenticator.png "下载Authenticator")  

③回到①，点击`Set up authenticator`  
![绑定Authenticator](/assets/images/问题排查/解决Gemini异常：SomethingWentWrong/6-SetUpAuthenticator.png "绑定Authenticator")   

④弹出二维码，用手机authenticator扫码  
![用验证器扫码](/assets/images/问题排查/解决Gemini异常：SomethingWentWrong/7-ScanQRCode.png "用验证器扫码")  

⑤app会给出一个验证码，写上去  
![填写Authenticator验证码](/assets/images/问题排查/解决Gemini异常：SomethingWentWrong/8-EnterCode.png "填写Authenticator验证码")  

⑥再`Turn on 2-Step Verification`  
![启动2步验证](/assets/images/问题排查/解决Gemini异常：SomethingWentWrong/9-TurnOnTwoStepVerification.png "启动2步验证")  

⑦他会要求`Get backup codes`，把备份码下载保存即可  
![保存备份码](/assets/images/问题排查/解决Gemini异常：SomethingWentWrong/10-BackupCodes.png "保存备份码")  

⑧完成  
![2步验证设置完成](/assets/images/问题排查/解决Gemini异常：SomethingWentWrong/11-Done.png "2步验证设置完成")  


（5）从<u>https://gemini.google.com/gems/create?hl=en-US&pli=1</u>（创建智能体）这里间接进入Gemini  
![绕路走](/assets/images/问题排查/解决Gemini异常：SomethingWentWrong/12-IndirectEntry.png "绕路走")  

点击`New chat`后就可以正常对话了（之后也可以从首页进去了）
![开启对话](/assets/images/问题排查/解决Gemini异常：SomethingWentWrong/13-NewChat.png "开启对话")  

我的小号先是直接进Gemini首页，没成功，然后从上面那个网址进去就行了



## 4、补充
（1）除了上面的设置，记得检查**年龄**、**`Recovery phone`**，反正`Personal info`和`Security & sign-in`以及各种设置都检查一遍，确定要求`Verify`的都验证了

（2）语言似乎没有限制，我老号English( United States )，小号简体中文，都可以用，但是还是用英语更安全一点

（3）IP用的美国，可以在<u>https://ping0.cc/ip/</u>检测自己的IP风控值，尽量固定用最稳定干净的那个

我最“干净”的美国IP也只能到这里了🤣（然而也过了）：  
> IP类型: IDC机房IP  
风控值: 40% 轻微风险  
原生IP: 原生IP  
大模型检测: 家庭宽带的概率为20%，可能为商业宽带或者机房宽带  
共享人数: 1000 - 10000 (高危)  

（4）防封号（可以问GPT/Grok）
* 平时**注意养号**，偶尔看看Gmail、Google/Chrome、Gplay什么的；
* 尽量固定用（相对）稳定干净的IP，**不频繁切IP**；
* 刚注册的Google号可以养几天（新手期容易被封）再轻度使用Gemini（问简单问题），慢慢过渡
* **装成正常的美国用户**，比如模仿IP所在地的作息，在当地时间用Gemini（这也是AI建议的，好像也有点道理🤔）

（5）<u>[Google Account](https://myaccount.google.com/)</u>首页可能会提醒设置住址、绑卡，我老号点了`dismiss`，小号没管，可能不管他会安全一点

然后`Security & sign-in`最上面有个`Security Checkup`，他有个“增强型安全浏览”（`Enhanced Safe Browsing`），我怕开了会识破我的伪装（虽然他肯定早就知道了😅）    
![安全检查](/assets/images/问题排查/解决Gemini异常：SomethingWentWrong/14-SecurityCheckup.png "安全检查")  

（6）知乎/浏览器搜`Gemini Something Went Wrong`可以找到一堆文章，但是感觉新发布的要好一点



## 5、结语
本来准备从Edge转移到Chrome，用新的QQ邮箱或者微软的outlook邮箱，以及换更好的订阅来注册新Google号，不过不确定用原来的手机号行不行（换号码的话，虚拟手机号只是临时用，可能还要长期租号）……反正也是一堆麻烦😵，还好补全设置后就过了。目前轻度用一下Gemini，再观察几天、检查下需不需要其他设置

---

> 参考文章  
<u>[Something went wrong解决方法](https://zhuanlan.zhihu.com/p/1995194901005104334)</u>

