# IDEA`Maven`项目打包


---

## 1. 背景
Maven项目，想要打包发给同学试一下，于是跟着AI的教程打包



## 2. 添加插件
Maven项目直接`package`，只会把`src/main`打成Jar包（甚至没有`src/test`），要把第三方依赖（`pom.xml`里面的依赖）弄进来，就需要添加`maven-shade-plugin`或者其他插件，再打成`FatJar`

在`pom.xml`添加`maven-shade-plugin`打包插件（放到`<dependencies>`后面; 在<u>[中央仓库](https://mvnrepository.com/artifact/org.apache.maven.plugins/maven-shade-plugin)</u>找最新版），然后点击右上角的同步图标(`Sync maven changes`)
```xml
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.6.1</version> <!-- 在中央仓库找最新版 -->
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                        <configuration>
                            <transformers>
                                <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                                    <mainClass>com.公司名.项目名.主类名(也就是主类的全路径)</mainClass>
                                </transformer>
                            </transformers>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
```



## 3. 打Jar包
IDEA右侧菜单栏 → `Maven` → 选择目标项目 → `Lifecycle` → `package`打包  
![IDEAMaven打Jar包](/assets/images/技术教程类/项目打包/IDEAMaven项目打包/01-Package.png "IDEAMaven打Jar包")

构建时会有警告，不影响。生成的`FatJar`在`target`文件夹下
* `artifactId-version.jar`
* `original-artifactId-version.jar`
* `artifiactId`、`version`都是`pom.xml`里面设定的项目名、版本

如果重复`package`，其他jar包不变，只会多一个`artifactId-version-shaded.jar`



## 4. 剪裁环境
如果有Java环境的话，打出`FatJar`就够了，如果想发给没有Java环境的同学，还需要剪裁Java运行环境(`Java Runtime Environment`)，拼在一起


### 4.1 分析依赖
先查看目标jar包需要哪些Java环境  
> 备注：文件资源管理器 → 地址栏输入cmd或者在该目录下右键然后打开终端 → 来到当前目录的命令行  
> 或者`Win + R` → 输入`cmd` → `Enter` → 输入`D:`切换到D盘 → 输入`cd 目录`切换目录

```bash
:: 命令行/.bat文件，用^来换行，注意^后不能有空格、直接换行
:: PowerShell用反引号换行(^是异或)
jdeps ^
--ignore-missing-deps ^
--print-module-deps ^
目标jar包的相对路径
```
> 备注：`.`、`./`为当前目录，`..`为上一级目录


### 4.2 剪裁环境
命令行会吐出一行内容，复制下来。现在开始剪裁Java运行环境
```bash
jlink ^
--add-modules 刚刚复制的内容 ^
--output 用来放Java环境的文件夹的相对路径 ^
--strip-debug ^
:: Java21+用的compress zip-0~9，数字表示压缩程度
:: java17及以前用的compress 0~2，compress 2 = compress zip-6
--compress zip-9 ^
--no-header-files ^
--no-man-pages
```



## 5. 打`JPackage`

### 5.1 配置 `WiX Toolset` 
这是`Jpackage`的打包工具，详见文章👉<u>[配置`WiX Toolset`](/技术教程类/项目打包/配置WiXToolset.md)</u>


### 5.2 现在开始拼接`FatJar`和`JRE`
```bash
jpackage ^
  :: 生成.exe安装器
  :: --type exe ^
  :: 生成无需安装的程序文件夹(此时不能有win-shortcut、win-menu)
  --type app-image ^
  --name "软件名" ^
  --app-version 版本号 ^
  :: 设置文件的图标（png、jpeg需要转成特定文件，如ico）
  --icon 图标的路径 ^
  :: 注意input会把指定文件夹的所有东西装进去，需要提前清理，只保留Jar包
  --input 存放Jar包的（相对）路径 ^
  --main-jar 入口jar包的名字 ^
  --main-class com.公司名.项目名.主类的名称 ^
  --runtime-image 打包用到的Java运行环境（JRE）的（相对）路径 ^
  :: 安装后创建快捷方式
  --win-shortcut ^
  :: 在 Windows 的“开始菜单”里创建一个程序组
  --win-menu ^
  :: jpackage 默认 GUI 程序，必须指定为控制台程序(如果是)
  --win-console ^
  :: 强制 JVM 内部所有 IO 流使用 UTF-8，防止读写文件或处理字符串时出现乱码
  --java-options "-Dfile.encoding=UTF-8" ^
  :: 专门针对标准输出（控制台打印）
  --java-options "-Dsun.stdout.encoding=UTF-8" ^
  :: 专门针对报错输出
  --java-options "-Dsun.stderr.encoding=UTF-8" ^
  --dest 输出的目标路径
```


### 5.3 这里是连贯的示例
* `jpackage_input`: 放`FatJar`，作为输入
* `MyProject.jar`: Jar包，放在`jpackage_input`里面
* `MyProject-jre`: 放`jlink`剪裁的`JRE`
* `jpackage_output`: 放打出的结果（输出）
* 注意命令行切换到当前目录
```bash
jdeps ^
--ignore-missing-deps ^
--print-module-deps ^
./jpackage_input/MyProject.jar

jlink ^
--add-modules 依赖模块 ^
--output ./MyProject-jre ^
--strip-debug ^
--compress zip-9 ^
--no-header-files ^
--no-man-pages

jpackage ^
  --type app-image ^
  --name "MyProject" ^
  --app-version 1.0 ^
  --icon ./logo.ico ^
  --input  ./jpackage_input^
  --main-jar MyProject.jar ^
  --main-class com.xxx.yyy.Main^
  --runtime-image ./MyProject-jre ^
  --win-console ^
  --java-options "-Dfile.encoding=UTF-8" ^
  --java-options "-Dsun.stdout.encoding=UTF-8" ^
  --java-options "-Dsun.stderr.encoding=UTF-8" ^
  --dest ./jpackage_output

jpackage ^
  --type exe ^
  --name "MyProject" ^
  --app-version 1.0 ^
  --icon ./logo.ico ^
  --input  ./jpackage_input^
  --main-jar MyProject.jar ^
  --main-class com.xxx.yyy.Main ^
  --runtime-image ./MyProject-jre ^
  --win-shortcut ^
  --win-menu ^
  --win-console ^
  --java-options "-Dfile.encoding=UTF-8" ^
  --java-options "-Dsun.stdout.encoding=UTF-8" ^
  --java-options "-Dsun.stderr.encoding=UTF-8" ^
  --dest ./jpackage_output
```


### 5.4 批处理脚本
以上模板还可以整理成全自动脚本，直接运行。只需要把下面的代码复制保存到`.txt`文本文件，然后改参数（Jar包名、文件夹名、主类名）、改文件后缀为`.bat`变成`batch`批处理文件，然后注意地址目录，就可以自动运行命令
```bash
@echo off
chcp 65001 >nul
setlocal EnableDelayedExpansion

echo ========================================
echo    MyProject - 全自动打包脚本
echo ========================================
echo.

:: 切换到脚本所在目录（通常用于命令行在非脚本目录调用脚本）
cd /d "%~dp0"
echo [INFO] 工作目录: %CD%
echo.


echo [STEP 1/4] 分析 JAR 依赖模块...

:: 先把 jdeps 输出存到临时文件，再读取最后一行（模块列表）
jdeps --ignore-missing-deps --print-module-deps ^
./jpackage_input/MyProject.jar > temp_modules.txt

if errorlevel 1 (
    echo [ERROR] jdeps 分析失败！
    del temp_modules.txt 2>nul
    pause
    exit /b 1
)

:: 读取文件内容到变量（这就是检测到的依赖模块）
set /p MODULES=<temp_modules.txt
del temp_modules.txt

echo [OK] 检测到依赖模块: %MODULES%
echo.


echo [STEP 2/4] 剪裁 JRE...
echo [INFO] 使用模块: %MODULES%

jlink ^
--add-modules %MODULES% ^
--output ./MyProject-jre ^
--strip-debug ^
--compress zip-9 ^
--no-header-files ^
--no-man-pages

if errorlevel 1 (
    echo [ERROR] jlink 构建 JRE 失败！
    pause
    exit /b 1
)
echo [OK] 自定义 JRE 构建完成: ./MyProject-jre
echo.


echo [STEP 3/4] 打包为 APP-IMAGE...
jpackage ^
  --type app-image ^
  --name "MyProject" ^
  --app-version 1.p ^
  --icon ./logo.ico ^
  --input  ./jpackage_input^
  --main-jar MyProject.jar ^
  --main-class com.xxx.yyy.Main^
  --runtime-image ./MyProject-jre ^
  --win-console ^
  --java-options "-Dfile.encoding=UTF-8" ^
  --java-options "-Dsun.stdout.encoding=UTF-8" ^
  --java-options "-Dsun.stderr.encoding=UTF-8" ^
  --dest ./jpackage_output

if errorlevel 1 (
    echo [ERROR] App-Image 打包失败！
    pause
    exit /b 1
)
echo [OK] App-Image 打包完成
echo.


echo [STEP 4/4] 打包为 EXE 安装器...
jpackage ^
  --type exe ^
  --name "MyProject" ^
  --app-version 1.0 ^
  --icon ./logo.ico ^
  --input  ./jpackage_input^
  --main-jar MyProject.jar ^
  --main-class com.xxx.yyy.Main ^
  --runtime-image ./MyProject-jre ^
  --win-shortcut ^
  --win-menu ^
  --win-console ^
  --java-options "-Dfile.encoding=UTF-8" ^
  --java-options "-Dsun.stdout.encoding=UTF-8" ^
  --java-options "-Dsun.stderr.encoding=UTF-8" ^
  --dest ./jpackage_output

if errorlevel 1 (
    echo [ERROR] EXE 安装程序打包失败！
    pause
    exit /b 1
)
echo [OK] EXE 安装程序打包完成
echo.

echo ========================================
echo    全自动构建完成！
echo    输出目录: ./jpackage_output
echo ========================================
pause
```

---


