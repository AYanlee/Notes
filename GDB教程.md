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

---

## 以下来自Operating System From 0 to 1

###### command： run

在此之前，要用gdb进行debug，首先要加载文件，有两种方法：

* 直接执行gdb命令，然后在进入gdb之后执行：file <filename>
* 执行gdb filename（可执行文件，最好带-g编译）

```
(gdb)  r 
```

就可以让文件运行起来，但是如果没有断点或者`int 3`的汇编指令（interrupt而非定义整数3）会执行到最后.

```c
1 #include <stdio.h>
2
3 int main(int argc, char *argv[])
4 {
5 printf("Hello World!\n");
6 return 0;
7 }
```

正常结束显示：

---

`Starting program: /tmp/hello
Hello World!
[Inferior 1 (process 1002) exited normally]`

---

###### command ：break / b

```
(gdb) b 3           //在第三行设置断点
```

执行`(gdb) r`显示信息：

---

`Starting program: /tmp/hello
Breakpoint 1, main (argc=1, argv=0x7fffffffdfb8) at hello.c:5
5 printf("Hello World!\n");`

---

一个断点，在hello.c的main函数中，但是停在第五行（停在下一个可执行的语句之前）

删除断点：

* d：删除所有断点
* d  num：删除第num个断点

###### command： next/n

这个指令执行当前行并且停在下一行，如果这一行是一个函数调用，不会进入到这个函数内部，会直接越过函数（依旧会执行，但不会显示调用函数内部详细信息）。

依旧是`hello.c`的例子：

```
(gdb) b 5     //在第5行处设置断点
```

然后依次执行：

```
(gdb) r 
(gdb) n
```



然后会输出以下内容：

---

```
Hello World!
6 return 0;          //这一行显示gdb停在什么地方
```

---

###### Command ： step / s

与next类似，执行当前行命令，停留在下一行。但当前行若为函数调用，则进入函数调用内部。

```c
#include <stdio.h>

int add(int a, int b) {
return a + b;
}

int main(int argc, char *argv[])
{
add(1, 2);
printf("Hello World!\n");
return 0;
}
```

执行：

```
(gdb) b main           //在main函数处设置断点
(gdb) r
(gdb) s
```

在执行完step（s）之后输出内容：

---

```
add (a=1, b=2) at hello.c:6
6 return a + b;     //当前停在第6行 return 处
```

---

###### command ： ni

这个指令每次执行一个汇编指令（首先我们要知道我们每一个高级语言指令都是由一个或者多个汇编语言构成的），ni与n都是执行一条指令，但是n执行一条高级语言，而ni执行一条低级汇编语言；

命令：`disassemble /m <function_name>` 例如还是上述调用一个add函数的例子：

```
disassemble /m main      //输出目标的汇编代码，/m参数是将汇编代码于高级语言语句相对应
```

![image-20220811090757214](F:\ayanlee\blog_file\gitlearn\learngit\GDB教程.assets\image-20220811090757214.png)

![image-20220811090819006](F:\ayanlee\blog_file\gitlearn\learngit\GDB教程.assets\image-20220811090819006.png)

上图对比可知有无参数m的区别（可能存在一些gdb版本中/m参数为/s）；

![image-20220811092326383](F:\ayanlee\blog_file\gitlearn\learngit\GDB教程.assets\image-20220811092326383.png)

可以说明很多高级语言的一个代码行会由一行或多行汇编代码实现

###### command ： si

si 和 ni 的关系就像s和n 的关系，不再展开；

###### command ： x

这个命令检查给定内存范围的内容。

![image-20220811102306281](F:\ayanlee\blog_file\gitlearn\learngit\GDB教程.assets\image-20220811102306281.png)

不加参数就会显示单一内存地址



###### Command ： print/p

可以将运行时文件相关变量打印出来

###### command ： until

执行的下一行内容，下一行行数一定比当前行大：多用于循环的调试，

```c
#include <stdio.h>


int add1000() 
{
	int total = 0;

	for (int i = 0; i < 1000; ++i)
	{
		total += i;
	}
	printf("Done adding!\n");
    
    return total;
}
int main(int argc, char *argv[])
{
	add1000();
	printf("Hello World!\n");
	return 0;
}

```

例如该例子：如果用步进命令s进入到add1000函数调用中，第一次执行到`total +=1`时，逻辑上会再一次执行for循环的判断语句：`for(int i = 0; i < 1000; ++i)`,但是用until语句会直接跳转到循环结束，也就是执行printf函数（当然循环也会执行完成，只不过跳过了每次执行的细节，直接到循环结束）

用法可以是执行`until`命令，在进入循环后执行；也可以用`until num`,可以到指定的确切行；



###### Command : finish

finish 命令执行到一个函数的结束，并且输出返回值

![image-20220811113230999](F:\ayanlee\blog_file\gitlearn\learngit\GDB教程.assets\image-20220811113230999.png)

命令`display`就是这么用的



###### Command ： bt

这个命令打印所有堆栈帧的back trace（回溯），看了例子就会理解：

