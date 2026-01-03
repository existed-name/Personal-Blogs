# 解决ClashVerge异常问题
---

### 1、配置  
* App：[ClashVerge2.4.4](https://github.com/clash-verge-rev/clash-verge-rev/releases/tag/v2.4.4)(目前最新版)
* 订阅：机场年订阅（HY2协议）
* 设置：规则模式 + 不开TUN，【节点选择】【漏网之鱼】代理组均选用美国节点
* 已经流畅使用6天


### 2、排查过程
1. 突然异常：
   1. 今天（2025/12/29/星期1）10点左右突然网络异常、**国内国外**网站都不能访问
   2. ClashVerge**无法查询IP信息**（除非关掉代理，只能查询真实IP）  
    ![无法查询IP](/assets/images/问题排查/解决ClashVerge异常问题/FailToChekIp.png)
   3. 然而节点测试**延迟并不高**  
      ![节点选择](/assets/images/问题排查/解决ClashVerge异常问题/Node-Selecting.png)  
      ![漏网之鱼](/assets/images/问题排查/解决ClashVerge异常问题/Fish-Escaping-From-Net.png)
   5. 切换美国6、意大利1、全球直连节点，或者开关TUN、开关IPV6，依然无法访问网页
   6. 并且在ClashVerge上更新订阅超时，可以导入其他文件，但不能切换订阅
  
2. 当时怀疑机场跑路了，于是赶紧去机场
   1. 我靠
      ![机场网页](/assets/images/问题排查/解决ClashVerge异常问题/CannotVisitWebiste.png)
   2. 哦，才反应过来，机场要挂梯子进去😂
   3. 用ClashForWindows + 免费节点可以访问机场以及国内外网站，虽然说不太稳定

3. 室友也是这个机场的年订阅 + ClashVerge2.3.0 + 【节点选择】自由选择，可以正常使用

4. 手机使用ClashMeta + 同一个订阅，可以正常访问Google、Chrome、GPT、GooglePlay商店

5. 综上，我怀疑并不是订阅出了问题，**而是ClashVerge或者电脑的问题**


### 3、结果
1. 关闭ClashVerge，21点钟再来看，可以用了
2. 突然又不可以用，重启，可以用……
3. 所以可能是我一直把它挂后台，不关App、电脑最多也就是“休眠”不是关机，然后就bug了——类似IDEA一直挂那里，然后有些功能有点问题，重启就好了
4. 事后问AI
   ![AI解释](/assets/images/问题排查/解决ClashVerge异常问题/GptExplain1.png)
