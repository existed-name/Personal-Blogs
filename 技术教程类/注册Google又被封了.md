# 注册Google又被封了😅


---

## 1、背景
才用2个星期，2月5号的时候Gemini突然把我ban了（虽然第2天又好了）  
![你的国家不支持](/assets/images/技术教程类/注册Google又被封了/01-GeminiNotSupport.png "你的国家不支持")  

虽然以前建了2个号，但想着分散风险，1个挂了还有其他的，肯定是多几个备用的好😂，于是又新建1个

然而又被封了😶这里记录下我的**注册过程、账号设置、申诉**，供各位参考



## 2、原因
事后分析，可能是因为
1. **机房I-P**: 虽然说我的美国I-P相对干净，但是终究不是家庭宽带/住宅I-P（然而太贵了😂）
2. **设备指纹**: GoogleApp可以读取的东西比较多，比如下载Gmail后它就已经知道我的手机已经有2个号，然后再加1个号进去，自然把这几个号联系在一起了
3. **一顿操作猛如虎**: 一系列安全设置有点“专业”、自动化
4. **换设备**: 新手期还没过去、刚注册就从手机转到电脑（陌生设备）
* 简单说，就是**不像真人**——比如注册完放一段时间/刷一会儿油管、保持同一个设备、慢慢安全设置


## 3、注册
### (1)电脑端+我的手机号
我跟往常一样用电脑注册，在手机扫码那里：手机拍照 → 相册里面识别二维码 → 打开链接（得爬楼梯）  
![电脑网页端注册](/assets/images/技术教程类/注册Google又被封了/02-CreateAccount.png "电脑网页端注册")  
![手机扫码](/assets/images/技术教程类/注册Google又被封了/03-ScanQRCode.png "手机扫码")  

然后Google会在手机上读取验证设备信息，结果他说我的手机号用了太多次了，让换个手机号（重试一下还是这样）
![换号码](/assets/images/技术教程类/注册Google又被封了/04-TryDifferentPhoneNumber.png "换号码")  


### (2)电脑端+别的手机号
结果扫码、进入链接后，它让我给它发短信——然而发送失败  
![短信发送失败](/assets/images/技术教程类/注册Google又被封了/05-FailToSendMsg.png "短信发送失败")  


### (3)手机端Gmail
后来我在知乎某个文章的评论看到：用手机端Gmail可以绕过“给对方发短信”这种操作

因为不想下东西，最开始我在手机电子邮件里面注册Gmail，但它比较怪异，2次都是填写到最后，准备完成注册，结果给我赶出来了

还是老老实实下载Gmail

进去过后 → `添加其他电子邮件地址` → `设置电子邮件`选Google → `创建账号`(`个人用途`) → 设置账号信息（姓、名、生日、邮箱），注意**生日填早点，确保成年**，然后同意1个条款就创建成功（确实**手机端Gmail比较松**，甚至还没有手机验证码）




## 4、设置
### (1)
然后点击`管理您的Google账号`，来到`Google Account`设置账号信息

根据经验(以前封号、<u>[]()</u>)，我首先去设置安全性（`安全性与登录`/`Security & sign-in`），提高账号安全权重  
![安全性设置](/assets/images/技术教程类/注册Google又被封了/06-SecuritySettings.png "安全性设置")  

* 那个`Google 提示`/`Google prompt`应该是默认开的，保持开启就行
* 添加`辅助电话号码`/`Recovery Phone`
* 添加`辅助邮箱`/`Recovery email`——手机端App确实比较松，号码、邮箱都不需要验证、直接添加就行
* `两步验证`/`2-Step Verification` 👇
![2步验证](/assets/images/技术教程类/注册Google又被封了/07-TwoStepVarify.png "2步验证")  


### (2)
先把`身份验证器`/`Authenticator`弄了：
* 手机应用商店下载`Microsoft Authenticator`(也叫`Authenticator`；或者Gplay商店下载`Google Authenticator`；<u>[GitHub学生认证]()</u>或者登录也可以用到)
* 然后进入`两步验证`的`设置身份验证器`(`Set up authenticator`)
![设置验证器](/assets/images/技术教程类/注册Google又被封了/08-SetupAuthenticator.png "设置验证器")  
* 把它给出的二维码截图、发到电脑/另一个设备上面打开
* 打开刚刚下载的手机`Authenticator`，右下角有个按钮，点一下，然后扫二维码
* 就绑上验证器了


### (3)
再弄`通行密钥和安全密钥`/`Passkeys and security keys`

根据经验，是在电脑浏览器（比如Edge）登录Google账号，然后让浏览器的密码管理器记住密码、用Windows Hello替换（我这里是PIN）——不过之后发现，还有手机上的，弄Google Account的时候，安卓系统会自动创建通行密钥（用指纹）

然后我就去电脑上面操作，专门开1个Profile放这个Google号、再设置下浏览器（<u>[]()</u>)。然而登录账号发现还是跑不了手机验证码，不过还好，是它给我发短信，验证后就进去了  
![手机验证码](/assets/images/技术教程类/注册Google又被封了/09-VerifyPhoneNumber.png "手机验证码")  



## 5、申诉
我以为没事了，后来发现创建账号后3个小时，就被封了
![封号斗罗](/assets/images/技术教程类/注册Google又被封了/10-Disabled.png "封号斗罗")  

* 第1个号是创建后2天被封: 手机Chrome注册，然后就没管了
* 第2个号是刚创建就被封: 电脑网页端注册、那个时候只需要对方给我发验证码
* 不过因为申诉都过了，所以我这次也申诉(`Start appeal`)

让AI根据自己的情况生成一个模板，略改一下提交
```java
Dear Google Support Team, my account was disabled shortly after creation. I am a real user, not a bot or program. I believe the system might have misidentified my account as a policy violation due to my unstable network connection (using a VPN for access). I have set up 2-step verification and linked my personal recovery information to ensure security. Please review my case and restore my access. Thank you for your help.
```

注意填写申诉的那个文字框，按`Shift + Enter`不可以换行，会跟`Enter`一样的效果（确认提交）

还好，初犯😵应该是机器审核，这3个号都是申诉后12个小时就解封了



## 6、附
现在就慢慢养号吧，每天发几个邮件、找AI问下注意事项，等几天再浅浅用1下Gemini，然后把这个号过渡到可以“正常用”Gemini的阶段

---

> **往期文章**  
> <u>[Edge防AI风控配置](/技术教程类/Edge防AI风控配置.md)</u>  
> <u>[解决Gemini异常：SomethingWentWrong](/问题排查/解决Gemini异常：SomethingWentWrong.md)</u>  
> <u>[Claude注册](/技术教程类/Claude注册.md)</u>  
> ……  
