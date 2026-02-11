# 解决Markdown文档图片缓存问题


---

## 1、问题描述
GitHub网页端删除了图片文件夹、修改这个文件夹的图片内容（但保持相同命名）、重新上传，Markdown文档里面的图片相对路径`![xxx](/path "xxx")`保持不变，发现Preview始终是旧图片，而不是新上传的图片，就算刷新网页也没用


## 2、原因
* 网页缓存还是原来的图片
![原因](/assets/images/问题排查/解决Markdown文档图片缓存问题/01-Causes.png "原因")  


## 3、解决方法
* 我是直接`Ctrl + F5`刷新
![解决方法](/assets/images/问题排查/解决Markdown文档图片缓存问题/02-Solutions.png "解决方法")  

备注：需要**先保存好自己写的东西，再刷新**。我是先写在记事本，再复制过去的，所以不怕刷新丢失进度。不过GitHub网页也有草稿备份，可以刷新后直接加载
![GitHub自带的备份](/assets/images/问题排查/解决Markdown文档图片缓存问题/03-Restore.png "GitHub自带的备份")  

---

> **往期文章**  
> <u>[网页端仓库管理](https://zhuanlan.zhihu.com/p/1929181706117678853)</u>  
> <u>[解决GitHub网络问题](https://zhuanlan.zhihu.com/p/1929183793639593440)</u>  
> <u>[GitHub学生认证](https://zhuanlan.zhihu.com/p/1929658519281436416)</u>  
> ……  
