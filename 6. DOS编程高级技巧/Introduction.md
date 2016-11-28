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
goto :eof
```

运行结果：

```
子程序返回值:"你好"
请按任意键继续...
```

