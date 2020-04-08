[目录](./) 

```powershell
gcc -g main.c -o test.out
```

> 生成用于调试的out文件

```powershell
gdb test.out
```

> 使用gdb打开out文件

|    命令     |                        功能                        |
| :---------: | :------------------------------------------------: |
|      r      | run， 直接调到断点处，没有设置断点的话直接运行程序 |
|    b fun    |     设置一个断点breakpoint在函数”fun”的最开始      |
|     b N     |          在当前运行源文件的第N行设置断点           |
| b file.c:N  |         在当前源文件file.c的第N行设置断点          |
|     d N     |               删掉delete第N行的断点                |
| info break  |                  显示所有断点信息                  |
|      c      | 继续(continue)运行程序，一直到下一个断点或程序结束 |
|      f      |           运行直到当前函数(function)结束           |
|      s      |            按step调试1行，会进入函数体             |
|     s N     |               按step调试接下来的N行                |
|      n      |     调试1行，与按s命令不同的是此处不进入函数体     |
|    p var    |              输出(print)变量”var”的值              |
| set var=val |                 设置变量”var”的值                  |
|     bt      |             打印调用堆栈(stack trace)              |
|      q      |                      退出gdb                       |
