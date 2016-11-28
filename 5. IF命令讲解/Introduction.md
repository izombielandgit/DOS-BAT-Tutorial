# 5. IF命令讲解

可以使用`if /?`查看IF的帮助。

* [5.1. ERRORLEVEL](#51-errorlevel)
* [5.2. string](#52-string)
* [5.3. EXIST](#53-exist)
* [5.4. IF的增强用法](#54-if的增强用法)

IF有三种基本的用法。

执行批处理程序中的条件处理。

```
IF [NOT] ERRORLEVEL number command
IF [NOT] string1==string2 command
IF [NOT] EXIST filename command
```

**NOT** 指定只有条件为false的情况下，Windows才应该执行该命令。

**ERRORLEVEL number** 如果最后运行的程序返回一个等于或大于指定数字的退出代码，指定条件为true。

**string1==string2** 如果指定的文字字符串匹配，指定条件为 true。

**EXIST filename** 如果指定的文件名存在，指定条件为 true。

**command** 如果符合条件，指定要执行的命令。如果指定的条件为FALSE，命令后可跟ELSE命令，该命令将在ELSE关键字之后执行该命令。

**ELSE** 子句必须出现在同一行上的IF之后。例如：

```
IF EXIST filename. (
    del filename.
) ELSE (
    echo filename. missing.
)
```

## 5.1. ERRORLEVEL

```
IF [NOT] ERRORLEVEL number command
```

基本作用是判断上一条命令执行结果的代码，以决定下一个步骤。

例：

```
@echo off
net user
IF %ERRORLEVEL%==0 echo net user 执行成功了!
pause
```

这是个简单判断上条命令是否执行成功。

这个用法和帮助里的用法不太一样，按照帮助里的写法`IF %ERRORLEVEL% == 0 echo net user 执行成功了!`这一句应该写成：`IF ERRORLEVEL 0 echo net user 执行成功了!`。但用这种语法，不管你的上面的命令是否执行成功，都会显示“执行成功”。

这是`IF ERRORLEVEL`语句的特点：当使用`IF ERRORLEVEL 0`的句式时，它的含义是：如果错误码的值大于或等于0的时候，将执行某个操作；当使用`IF %ERRORLEVEL%==0`的句式时，它的含义是：如果错误码的值等于0的时候，将执行某个操作。因为这两种句式含义的差别，如果使用前一种句式的时候，错误码语句的排列顺序是从大到小排列。`%ERRORLEVEL%`是系统变量，返回上条命令的执行结果代码。“成功”用0表示，“失败”用1表示。当然还有其他参数，用的时候基本就这两数字。

这只是一般的情况，实际上，ERRORLEVEL返回值可以在0~255之间。比如：xcopy默认的ERRORLEVEL值就有5个，分别表示5种执行状态：

```
退出码 说明
0      文件复制没有错误。
1      if errorlevel 2 echo。
2      用户按 CTRL+C 终止了 xcopy。
4      出现了初始化错误。没有足够的内存或磁盘空间，或命令行上输入了无效的驱动器名称或语法。
5      出现了磁盘写入错误。
```

要判断上面xcopy命令的5种退出情况，应写成：

```
if errorlevel 5 echo 出现了磁盘写入错误
if errorlevel 4 echo 出现了初始化错误
if errorlevel 2 echo 用户按 CTRL+C 终止了 xcopy
if errorlevel 1 echo if errorlevel 2 echo
if errorlevel 0 echo 文件复制没有错误。
```

才能正确执行。

再举几个例子：

```
@echo off
net usertest
IF %ERRORLEVEL%==1 echo net user 执行失败了!
pause
```

判断上一条命令是否执行失败的。

```
@echo off
set /p var=随便输入一个命令:
%var%
IF %ERRORLEVEL%==0 goto yes
goto no
:yes
echo "%var%"执行成功了.
pause
:no
echo "%var%"执行失败了.
pause
```

判断你输入的命令是成功还是失败了。

简化版：

```
@echo off
set /p var=随便输入一个命令:
%var%
IF %ERRORLEVEL%==0 (echo "%var%"执行成功了.) ELSE echo "%var%"执行失败了.
pause
```

`ELSE`后面写上执行失败后的操作。

当然我门还可以把IF ELSE这样的语句分成几行写出来,使他看上去好看点：

```
@echo off
set /p var=随便输入一个命令:
%var%
IF %ERRORLEVEL%==0 (
echo "%var%"执行成功了.
) ELSE (
echo "%var%"执行失败了.
)
pause
```

这里介绍的两种简写对IF的三种语法都可以套用,不只是在`IF [NOT] ERRORLEVEL number command`这种语法上才能使用。

## 5.2. string

```
IF [NOT] string1==string2 command
```

用来比较变量或者字符的值是不是相等的。

例：

```
@echo off
set /p var=请输入第一个比较字符:
set /p var2=请输入第二个比较字符:
IF %var% == %var2% (echo 我们相等) ELSE echo 我们不相等
pause
```

上面这个例子可以判断你输入的值是不是相等。但是如果输入相同的字符，而其中一个后面带一个空格，还是会认为相等，如何让有空格的输入不相等呢？我们在比较字符上加个双引号就可以了：

```
@echo off
set /p var=请输入第一个比较字符:
set /p var2=请输入第二个比较字符:
IF "%var%" == "%var2%" (echo 我们相等) ELSE echo 我们不相等
pause
```

## 5.3. EXIST

```
IF [NOT] EXIST filename command
```

判断某个文件或者文件夹是否存在的语法

例：

```
@echo off
IF EXIST "c:\test" (echo 存在文件) ELSE echo 不存在文件
pause
```

判断的文件路径加引号是为了防止路径有空格，如果路径有空格加个双引号就不会出现判断出错了。

另外我们看到每条IF用法后都有个`[NOT]`语句，加上的话，表示先判断我们的条件不成立时，没加他默认是先判断条件成立时，比如上面这个例改为：

```
@echo off
IF NOT EXIST "c:\test" (echo 存在文件) ELSE echo 不存在文件
pause
```

执行后，如果C盘下没有test,他还是会显示`存在文件`，这就表示加了NOT就
会先判断条件失败的情况。上面例子改成这样就正确了：

```
@echo off
IF NOT EXIST "c:\test" (echo 不存在文件) ELSE echo 存在文件
pause
```

## 5.4. IF的增强用法

```
IF [/I] string1 compare-op string2 command
IF CMDEXTVERSION number command
IF DEFINED variable command
```

后面两个用法，因为它们和上面的用法表示的意义基本一样，只简单说说。

`IF [/I] string1 compare-op string2 command`这个语句在判断字符时不区分字符的大小写。

`CMDEXTVERSION`条件的作用跟ERRORLEVEL的一样，除了它是在跟与命令扩展有关联的内部版本号比较。第一个版本是1。每次对命令扩展有相当大的增强时，版本号会增加一个。命令扩展被停用时，CMDEXTVERSION条件不是真的。

如果已定义环境变量，`DEFINED`条件的作用跟EXIST的一样，除了它取得一个环境变量，返回的结果是true。

例：

```
@echo off
if a == A (echo 我们相等) ELSE echo 我们不相等
pause
```

执行后显示：

```
我们不相等
请按任意键继续...
```

加上`/I`不区分大小写就相等了。

```
@echo off
if /i a == A (echo 我们相等) ELSE echo 我们不相等
pause
```

下面是一些用来判断数字的符号：

```
EQU - 等于
NEQ - 不等于
LSS - 小于
LEQ - 小于或等于
GTR - 大于
GEQ - 大于或等于
```

例：

```
@echo off
set /p var=请输入一个数字:
if %var% LEQ 4 (echo 我小于等于4) ELSE echo 我不小于等于4
pause
```