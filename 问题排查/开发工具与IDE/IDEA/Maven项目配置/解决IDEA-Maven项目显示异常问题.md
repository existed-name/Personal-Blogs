# 解决IDEA-Maven项目显示异常问题

> <u>[个人博客仓库](https://github.com/existed-name/Personal-Blogs)</u>
---

## 1、问题描述

IDEA创建SpringBoot项目（在一个`Project`下创建`Module`），包含依赖：`lombok`、`MySQL Driver`、`MyBatis Framework`

①`pom.xml`**图标显示异常**：本来应该是蓝色的`M`，显示为橙色`</>`

②启动类（`SpringbootXxxxApplication`）的图标显示异常：本来应该是一个“启动”标志，但是Java的茶杯logo；并且在项目栏里还多显示了`.java`后缀
![图标对比](/assets/images/问题排查/开发工具与IDE/IDEA/Maven项目配置/解决IDEA-Maven项目显示异常问题/01-IconComparison.png "图标对比")  

③这个项目里面的编译器**检测功能失效**，不能检查语法错误、警告/提醒，但其他项目可以正常检测
![取消语法高亮和自动检测](/assets/images/问题排查/开发工具与IDE/IDEA/Maven项目配置/解决IDEA-Maven项目显示异常问题/02-NoHighlighting.png "取消语法高亮和自动检测")  
![有语法高亮和自动检测](/assets/images/问题排查/开发工具与IDE/IDEA/Maven项目配置/解决IDEA-Maven项目显示异常问题/03-Highlighting.png "有语法高亮和自动检测")  

④重启IDEA还是出现上述问题

⑤在IDEA内，删除该项目，重建，还是异常（之前也有这个问题，但是重建后就正常了）

⑥创建另一个SpringBoot项目，名为`demo`，刚开始图标显示异常，在编译器自动`downloading plugins for demo`之后可以正常显示

⑦创建一系列项目后发现**跟依赖应该没有关系**：不包含、包含上面3个依赖，或者随便选依赖，最后都可能出现异常



## 2、解决方法
* 删除、重建项目（可能成功）

* 右键`pom.xml` → `Add as Maven Project`（本人立即见效）
![添加为Maven项目](/assets/images/问题排查/开发工具与IDE/IDEA/Maven项目配置/解决IDEA-Maven项目显示异常问题/04-AddAsMavenProject.png "添加为Maven项目")  

* Gemini给的方法挺有用的  
![拿下~](/assets/images/问题排查/开发工具与IDE/IDEA/Maven项目配置/解决IDEA-Maven项目显示异常问题/05-Solutions.png "拿下~")  

---
