# 6. DOS编程高级技巧

## 6.1. 交互界面设计

例：

```
@echo off
cls
title 终极多功能修复
:menu
cls
color 0A
echo.
echo ==============================
echo 请选择要进行的操作，然后按回车
echo ==============================
echo.
echo 1.网络修复及上网相关设置,修复 IE,自定义屏蔽网站
echo.
echo 2.病毒专杀工具，端口关闭工具,关闭自动播放
echo.
echo 3.清除所有多余的自启动项目，修复系统错误
echo.
echo 4.清理系统垃圾,提高启动速度
echo.
echo Q.退出
echo.
echo.
:cho
set choice=
set /p choice= 请选择:
IF NOT "%choice%"=="" SET choice=%choice:~0,1%
if /i "%choice%"=="1" goto ip
if /i "%choice%"=="2" goto setsave
if /i "%choice%"=="3" goto kaiji
if /i "%choice%"=="4" goto clean
if /i "%choice%"=="Q" goto end
echo 选择无效，请重新输入
echo.
goto cho
:ip
echo 您选择了 1
pause&goto menu
:setsave
echo 您选择了 2
pause&goto menu
:kaiji
echo 您选择了 3
pause&goto menu
:clean
echo 您选择了 4
pause&goto menu
:end
echo 点击任意键退出&pause>nul&goto :eof
```

只要学完本教程前面的章节，上面的程序应该能看懂了。

## 6.2. IF...ELSE...条件语句

前面已经谈到，DOS 条件语句主要有以下形式

```
IF [NOT] ERRORLEVEL number command
IF [NOT] string1==string2 command
IF [NOT] EXIST filename command
```

增强用法：

```
IF [/I] string1 compare-op string2 command
```

增强用法中加上`/I`就不区分大小写了。

增强用法中还有一些用来判断数字的符号：

```
EQU - 等于
NEQ - 不等于
LSS - 小于
LEQ - 小于或等于
GTR - 大于
GEQ - 大于或等于
```

上面的`command`命令都可以用小括号来使用多条命令的组合，包括ELSE子句。组合命令中可以嵌套使用条件或循环命令。

例：

```
IF EXIST filename (
del filename
) ELSE (
echo filename missing
)
```

也可写成：

```
IF EXIST filename (del filename) ELSE (echo filename missing)
```

但这种写法不适合命令太多或嵌套命令的使用。

## 6.3. 循环语句

### 6.3.1. 指定次数循环

```
FOR /L %variable IN (start,step,end) DO command [command-parameters]
```

组合命令：

```
FOR /L %variable IN (start,step,end) DO (
Command1
Command2
......
commandN
)
```

### 6.3.2. 对某集合执行循环语句

```
FOR %%variable IN (set) DO command [command-parameters]
```

`%%variable`指定一个单一字母可替换的参数。

`(set)`指定一个或一组文件。可以使用通配符。

`command`对每个文件执行的命令，可用小括号使用多条命令组合。

```
FOR /R [[drive:]path] %variable IN (set) DO command [command-parameters]
```

检查以`[drive:]path`为根的目录树，指向每个目录中的FOR语句。如果在`/R`后没有指定目录，则使用当前目录。如果集仅为一个单点(.)字符，则枚举该目录树。同前面一样，`command`可以用括号来组合：

```
FOR /R [[drive:]path] %variable IN (set) DO (
Command1
Command2
......
commandN
)
```

### 6.3.3. 条件循环

利用goto语句和条件判断，DOS可以实现条件循环，例：

```
@echo off
set var=0
rem ************循环开始了
:continue
set /a var+=1
echo 第%var%次循环
if %var% lss 100 goto continue
rem ************循环结束了
echo 循环执行完毕
pause
```

### 6.3.4. 子程序

在批处理程序中可以调用外部可运行程序，比如exe程序，也可调用其他批处理程序，这些也可以看作子程序，但是不够方便，如果被调用的程序很多，就显得不够简明了，很繁琐。

批处理可以调用本程序中的一个程序段，相当于子程序，这些子程序一般放在主程序后面。

子程序调用格式：

```
CALL :label arguments
```

子程序语法：

```
:label
command1
command2
......
commandN
goto :eof
```

传至子程序的参数在CALL语句中指定，在子程序中用%1、%2 至%9 的形式调用，而子程序返回主程序的数据只需在调用结束后直接引用就可以了，当然也可以指定返回变量，请看下面的例子。

子程序例1：

```
@echo off
call :sub return 你好
echo 子程序返回值:%return%
pause
:sub
set %1="%2"
rem %1即return，%2即你好
goto :eof
```

运行结果：

```
子程序返回值:"你好"
请按任意键继续...
```

子程序例2：（求多个整数相加的子程序）

```
@echo off
set sum=0
call :sub sum 10 20 35
echo 数据求和结果:%sum%
pause
:sub
rem 参数1为返回变量名称
set /a %1+=%2
shift /2
if not "%2"=="" goto sub
goto :eof
```

运行结果：

```
65
```

在Win98系统中，不支持上面这种标号调用，须将子程序单独保存为一个批处理程序，然后调用。

### 6.3.5. 用ftp命令实现自动下载

ftp是常用的下载工具，ftp界面中有40多个常用命令，不在这里介绍。这里介绍
如何用DOS命令行调用ftp命令，实现ftp自动登录，上传下载，并自动退出ftp程序。其实可以将ftp命令组合保存为一个文本文件，然后用以下命令调用即可。

```
ftp -n -s:[[drive:]path]filename
```

上面的`filename`为ftp命令文件，包括登录IP地址，用户名，密码，操作命令等

例：

```
open 90.52.8.3  #打开ip
user username   #用户为username
password1234    #密码
bin             #二进制传输模式
prompt
cd tmp1         #切换至username用户下的tmp1目录
pwd
lcd d:\download #本地目录
mget *          #下载tmp1目录下的所有文件
bye             #退出ftp
```

笔者注：现在很少用ftp了吧，就当参考思路。

### 6.3.6. 用7-ZIP实现命令行压缩和解压功能

语法格式：（详细情况见7-zip帮助文件，如果有难度可以先跳过，用到的时候再学）

```
7z <command> [<switch>...] <base_archive_name> [<arguments>...]
```

7z.exe的每个命令都有不同的参数`<switch>`，请看帮助文件

`<base_archive_name>`为压缩包名称

`<arguments>`为文件名称，支持通配符或文件列表

其中，`7z`是指命令行压缩解压程序7z.exe，`<command>`是7z.exe包含的命令，列举如下：

**a**：Adds files to archive. 添加至压缩包

a命令可用参数：

```
-i (Include)
-m (Method)
-p (Set Password)
-r (Recurse)
-sfx (create SFX)
-si(use StdIn)
-so (use StdOut)
-ssw (Compressshared files)
-t (Type of archive)
-u (Update)
-v (Volumes)
-w (Working Dir)
-x (Exclude)
```

**b**：Benchmark

**d**：Deletes files from archive. 从压缩包中删除文件

d命令可用参数：

```
-i (Include)
-m (Method)
-p (Set Password)
-r (Recurse)
-u (Update)
-w (Working Dir)
-x (Exclude)
```

**e**：Extract 解压文件至当前目录或指定目录

e命令可用参数：

```
-ai (Include archives)
-an (Disable parsing of archive_name)
-ao (Overwrite mode)
-ax (Exclude archives)
-i (Include)
-o (Set Output Directory)
-p (Set Password)
-r (Recurse)
-so (use StdOut)
-x (Exclude)
-y (Assume Yes on all queries)
```

**l**：Lists contents of archive.

**t**：Test

**u**：Update

**x**：eXtract with full paths 用文件的完整路径解压至当前目录或指定目录

x命令可用参数：

```
-ai (Include archives)
-an (Disable parsing of archive_name)
-ao (Overwrite mode)
-ax (Exclude archives)
-i (Include)
-o (Set Output Directory)
-p (Set Password)
-r (Recurse)
-so (use StdOut)
-x (Exclude)
-y (Assume Yes on all queries)
```

### 6.3.7. 调用 VBScript 程序

使用Windows脚本宿主，可以在命令提示符下运行脚本。CScript.exe提供了用于设置脚本属性的命令行开关。

### 6.3.8. 
### 6.3.9. 
### 6.3.10. 
