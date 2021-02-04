?>深入理解计算机系统

## 计算机系统漫游

系统中的所有信息都是由bit串构成(明文->编码->01),信息的解释取决于具体的context

C程序编译过程:预处理->编译->汇编->链接

```cpp
#include<stdio.h>
int main(){
    printf("Hello World\n");
    return 0;
}
```

`gcc -o hello hello.c`

- 预处理阶段

预处理器根据以字符`#`开头的语句,修改原始的C程序

`#include<stdio.h>`使预处理器读取系统头文件`stdio.h`,并将其插入,得到`hello.i`

`gcc -E -o hello.i hello.c`

```cpp
# 1 "hello.c"
# 1 "<built-in>"
# 1 "<命令行>"
# 31 "<命令行>"
# 1 "/usr/include/stdc-predef.h" 1 3 4
# 32 "<命令行>" 2
# 1 "hello.c"
# 1 "/usr/include/stdio.h" 1 3 4
...
typedef unsigned char __u_char;
typedef unsigned short int __u_short;
typedef unsigned int __u_int;
typedef unsigned long int __u_long;


typedef signed char __int8_t;
typedef unsigned char __uint8_t;
typedef signed short int __int16_t;
typedef unsigned short int __uint16_t;
typedef signed int __int32_t;
typedef unsigned int __uint32_t;

typedef signed long int __int64_t;
typedef unsigned long int __uint64_t;
...
extern void funlockfile (FILE *__stream) __attribute__ ((__nothrow__ , __leaf__));
# 857 "/usr/include/stdio.h" 3 4
extern int __uflow (FILE *);
extern int __overflow (FILE *, int);
# 874 "/usr/include/stdio.h" 3 4

# 2 "hello.c" 2

# 2 "hello.c"
int main(){
    printf("Hello World\n");
    return 0;
}
```

- 编译阶段

编译器将`hello.i`翻译成`hello.s`,其内容为汇编语言

`gcc -S -o hello.s hello.i`

```nasm
	.file	"hello.c"
	.text
	.section	.rodata
.LC0:
	.string	"Hello World"
	.text
	.globl	main
	.type	main, @function
main:
.LFB0:
	.cfi_startproc
	pushq	%rbp
	.cfi_def_cfa_offset 16
	.cfi_offset 6, -16
	movq	%rsp, %rbp
	.cfi_def_cfa_register 6
	leaq	.LC0(%rip), %rdi
	call	puts@PLT
	movl	$0, %eax
	popq	%rbp
	.cfi_def_cfa 7, 8
	ret
	.cfi_endproc
.LFE0:
	.size	main, .-main
	.ident	"GCC: (GNU) 10.2.0"
	.section	.note.GNU-stack,"",@progbits
```

- 汇编阶段

汇编器将`hello.s`翻译成机器语言指令,将这些指令打包成**可重定位目标程序**的格式,并保存为`hello.o`的二进制文件

`gcc -c hello.s -o hello.o`

- 链接阶段

该程序调用了`printf`函数,该函数存在与一个名为`printf.o`的**单独**的**已预编译**的目标文件中,这个文件要和`hello.o`进行合并

链接器负责执行这种合并,得到名为`hello`的可执行文件

`gcc hello.o -o hello`

---

**进程**是操作系统对一个正在运行的程序的一种抽象,在一个操作系统上可以同时运行多个进程,**并发运行**则是说一个进程的指令和另一个进程的指令交错执行

操作系统保持跟踪进程运行所学的所有状态信息,该状态称为**上下文**

一个进程可以由多个**线程**组成,每个线程都运行在进程的上下文中,共享同样的代码和全局数据

---

**虚拟内存**是一个抽象概念,其为每个进程提供了一个假象,即每个进程都在独占地使用内存,每个进程看到的内存都是一直的,称为**虚拟地址空间**

在Linux中,地址空间最上面的区域是保留给操作系统中的代码与数据,对所有进程一致,底部区域存放用户进程定义的代码和数据,地址**从下往上增大**

![](https://img.misaka.gq/Notes/CSAPP/虚拟地址空间.png)

- 程序代码和数据

对所有进程来说,代码是从同一固定地址开始,紧接着是和全局变量相对应的数据位置,代码和数据区是直接按照可执行文件的内容初始化的

- 堆

代码和数据区后便是堆,代码和数据区在进程一开始便被指定了大小,而堆可以通过`malloc`和`free`等函数进行动态调整

- 共享库

存放如C标准库之类的共享库的代码和数据区域

- 栈

位于**用户虚拟地址空间**顶部的就是**用户栈**,编译器用其实现函数调用,用户栈在程序执行期间也可以动态调整大小,每次调用一个函数时,用户栈增长,从一个函数返回时,用户栈收缩

- 内核虚拟内存

位于**地址空间**顶部的区域是为内核保留,不允许应用程序读写该区域的内容或者直接调用内核代码定义的函数,只能通过调用内核来执行这些操作

---

操作系统内核是应用程序和硬件之间的媒介,提供了三个基本的抽象

1. 文件是对I/O设备的抽象

2. 虚拟内存是对内存和磁盘的抽象

3. 进程是处理器,内存和I/O设备的抽象

## 信息的处理和表示

十进制转16进制(辗转相除法)

`314156=19634*16+12`(C)

`19634=1227*16+2`(2)

`1227=76*16+11`(B)

`76=4*16+12`(C)

`4=0*16+4`(4)

314156=0x4CB2C

---

每台计算机都有一个**字长**,指明指针数据的标称大小,因为虚拟地址是以一个字来编码的,所以字长决定了虚拟地址的空间的最大大小

对于一台字长为`w`位的机器,虚拟地址的范围是$0$~$2^w-1$,程序最多访问$2^w$个字节