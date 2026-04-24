# `MyBatis`配置`Mapper.xml`模板

---

## 1、描述
`Mapper.xml`文件需要配置`DTD约束`，每次都复制粘贴会比较麻烦，于是根据AI提示配置了模板，这样每次创建`Mapper.xml`后就只管写SQL了

## 2、配置步骤
### （1）配置模板
IDEA菜单栏 → File → Settings → Editor → `File and Code Templates` 中 → 点击`+`(新建模板) → 取名(`Name`)`MyBatis Mapper`，扩展名(`Extension`)`xml`

把这个模板粘进去  
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="${PACKAGE_NAME}.${NAME}">

</mapper>
```
![配置展示](/assets/images/技术教程类/框架使用/MyBatis/MyBatis配置Mapper.xml模板/01-Settings.png "配置展示")  

### （2）创建文件
选择1个文件(夹) → 右键后点击`New`(也可以直接`Alt + Shift + Insert`) → `MyBatis Mapper`  
![创建文件](/assets/images/技术教程类/框架使用/MyBatis/MyBatis配置Mapper.xml模板/02-NewFile.png "创建文件")  
![命名不需要带文件后缀](/assets/images/技术教程类/框架使用/MyBatis/MyBatis配置Mapper.xml模板/03-UserMapper.png "命名不需要带文件后缀")  

甚至命名空间都自动生成了  
![自动化~~~](/assets/images/技术教程类/框架使用/MyBatis/MyBatis配置Mapper.xml模板/04-AutoTemplate.png "自动化~~~")  

---

