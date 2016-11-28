# 5. IF命令讲解

可以使用`if /?`查看IF的帮助。

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


































