### GDB教程



GDB完全深入掌握是一件不容易的事情，但是要做到处理日常程序调试并不难，以下内容是我所整合的gdb入门资料。

| 命令                                  | 解释                                                         |
| ------------------------------------- | ------------------------------------------------------------ |
| file <文件名>                         | 加载被调试程序的可执行文件，如果就在程序所在目录下进行调试，则不需要带路径 |
| r                                     | Run的缩写，运行被调试的程序。如果此前没有下过断点，则执行完整个程序；如果有断点，则程序暂停在第一可用断点处 |
| b <行号>  <br/>b<函数名称>    d[编号] | b：breakpoint的简写，设置断点。两可以使用“行号”“函数名称”“执行地址”等方式指定断点位置。<br/>d: Delete breakpoint的简写，删除指定编号的某个断点，或删除所有断点。断点编号从1开始递增。 |
| s, n                                  | s: 执行一行源程序代码，如果此行代码中有函数调用，则进入该函数；<br/>n: 执行一行源程序代码，此行代码中的函数调用也一并执行。<br/>s 相当于其它调试器中的“Step Into (单步跟踪进入)”；<br/>n 相当于其它调试器中的“Step Over (单步跟踪)”。<br/><br/>这两个命令必须在有源代码调试信息的情况下才可以使用（GCC编译时使用“-g”参数）。 |
| p <变量名称>                          | Print的简写，显示指定变量的值                                |
| display  <br/>undisplay <编号>        | display，设置程序中断后欲显示的数据及其格式。<br/>例如，如果希望每次程序中断后可以看到即将被执行的下一条汇编指令，可以使用命令<br/>“display /i $pc”<br/>其中 $pc 代表当前汇编指令，/i 表示以十六进行显示。当需要关心汇编代码时，此命令相当有用。<br/>undispaly，取消先前的display设置，编号从1开始递增。 |
| i                                     | Info的简写，用于显示各类信息，详情请查阅“help i”             |
| q                                     | Quit的简写，用于退出gdb调试环境                              |
| help [命令名称]                       | 帮助命令，提供对GDB各种命令的解释说明                        |

可以使用以下案例：

```
#include <stdio.h>

int nGlobalVar = 0;

int tempFunction(int a, int b)
{
    printf("tempFunction is called, a = %d, b = %d /n", a, b);
    return (a + b);
}

int main()
{
    int n;
    n = 1;
    n++;
    n--;

    nGlobalVar += 100;
    nGlobalVar -= 12;

    printf("n = %d, nGlobalVar = %d /n", n, nGlobalVar);

    n = tempFunction(1, 2);
    printf("n = %d", n);

    return 0;
}
```

接下来使用常用命令：

#### 既然我们要调试一个可执行文件，进入这个文件进行调试的方法：

* `gdb filename`,命令中的filename要是可执行文件
* 或者`gdb`进入编译环境之后，用命令：`file <filename>`

提示，进行编译的时候建议加上参数 -g

```
gcc filename -o objectname -g
```

（虽然在Linux中文件名的后缀不像Windows中那样有区分文件属性的功能，但是`gcc`命令却要求文件后缀是`.c`或者`.cpp`，如果编译报错，文件名是一个可能出错的地方）

然后执行`RUN`命令的简写`r`（由于没有加入任何断点，所以会执行到程序结束）

==如果在这个过程中出现 `Missing separate debuginfos, use: debuginfo-install glibc-2.17-325.el7_9.x86_64`,退出`gdb`调试环境，执行`use`之后的命令，下载所需内容==

#### 断点

在`main`函数前加一个断点的方法是：`b main`

会显示如下内容：`Breakpoint 1 at 0x804835c: file gdb-sample.c, line 19.`

* `Breakpoint 1` 意为第一个断点（断点是从1开始计数）
* `at 0x804835c`意为断点的地址
* file 就是文件
* line 是断点所在行

然后执行`r`会显示类似如下信息：`Breakpoint 1, main () at gdb-sample.c:19   19 n = 1;`

`19 和 n = 1`; 是接下来会执行的内容

通过浏览程序，我们可以知道断点在`n = 1`上面，可以得知`int n;`并不是可执行语句，而是变量定义语句

`s`命令执行下一行代码：`n = 1`，接下来也会显示下一次要执行内容的所在行以及内容

#### 查看变量内容

命令`print`的缩写`p`，后面加上要显示的变量名称即可：

`p n`或者`p nGlobalVar`等类似语法

#### 继续执行

命令`Continue`命令的简写`c`，继续执行调试程序，程序将执行到下一个断点处

#### 汇编指令

如果想要了解汇编执行的代码，可以用命令：

```
display /i $pc
```

* `display`命令用来显示数据内容
* `/i `代表 16进制
* `$pc`是程序指针，指向下一个要被执行的语句

#### info

i是`info`或者`information`的简写，可以展示信息。例如：

``` 
i r              // information Register
i r eax          // Information Register eax(显示指定寄存器内容)
```

退出：q

