### 编写并安装中断7ch的中断例程

1. 求一word型数据的平方

```nasm
assume cs:code
code segment
main:
    mov ax,cs
    mov ds,ax
    mov si,offset power
    
    mov ax,0000h
    mov es,ax
    mov di,0200h

    cld
    mov cx,offset powerend-offset power
    rep movsb

    mov ax,0000h
    mov es,ax
    mov word ptr es:[7ch*4],0200h
    mov word ptr es:[7ch*4+2],0000h

    mov ax,4c00h
    int 21h
power:
    mul ax
    iret;恢复ip,cs,flag
powerend:
    nop
code ends
end main
```

```nasm
assume cs:code
code segment
main:
    mov ax,3456
    int 7ch
    add ax,ax
    adc dx,dx
    
    mov ax,4c00h
    int 21h
code ends
end main
```

2. 将一个全是字母,以0结尾的字符串,转化为大写

```nasm
assume cs:code
code segment
main:
    mov ax,cs
    mov ds,ax
    mov si,offset int7c
    
    mov ax,0000h
    mov es,ax
    mov di,0200h

    cld
    mov cx,offset int7c_end-offset int7c
    rep movsb

    mov ax,0000h
    mov es,ax
    mov word ptr es:[7ch*4],0200h
    mov word ptr es:[7ch*4+2],0000h

    mov ax,4c00h
    int 21h
int7c:
    push si
    push cx
    mov cx,0
upper:
    mov cl,byte ptr ds:[si]
    jcxz int7c_finish
    and byte ptr ds:[si],11011111b
    inc si
    jmp upper
int7c_finish:
    pop cx
    pop si
    iret;恢复ip,cs,flag
int7c_end:
    nop
code ends
end main
```

```nasm
assume cs:code
data segment
    db 'wdnmd',0
data ends
code segment
main:
    mov ax,data
    mov ds,ax
    mov si,0
    int 7ch

    mov ax,4c00h
    int 21h
code ends
end main
```

3. 用7ch中断例程完成`loop`指令的功能

```nasm
assume cs:code
code segment
main:
    mov ax,cs
    mov ds,ax
    mov si,offset int7c
    
    mov ax,0000h
    mov es,ax
    mov di,0200h

    cld
    mov cx,offset int7c_end-offset int7c
    rep movsb

    mov ax,0000h
    mov es,ax
    mov word ptr es:[7ch*4],0200h
    mov word ptr es:[7ch*4+2],0000h

    mov ax,4c00h
    int 21h
int7c:
    push bp
    mov bp,sp
    dec cx
    jcxz finish
    sub ss:[bp+2],bx;将ip重新指向s
finish:
    mov sp,bp
    pop bp
    iret;恢复ip,cs,flag
int7c_end:
    nop
code ends
end main
```

```nasm
assume cs:code
code segment
main:
    mov ax,0b800h
    mov es,ax
    mov di,160*12

    mov bx,offset se-offset s
    mov cx,80
s:
    mov byte ptr es:[di],'!'
    add di,2
    int 7ch;中断时,ip指向se,flag,cs,ip入栈,在中断例程中如果cx!=0将栈中的ip通过bx修改为指向s,cx=0则不进行修改
se:
    nop

    mov ax,4c00h
    int 21h
code ends
end main
```

### BIOS中断例程
  
BIOS(基本输入输出系统),主要包含以下几部分内容

- 硬件系统的检测和初始化程序
- 外部中断和内部中断的中断例程
- 用于对硬件设备进行I/O操作的中断例程
- 其他和硬件系统相关的中断例程

BIOS中的中断例程在内存的安装方法

- CPU加电,初始化`CS=0FFFFH`,`IP=0`,自动从`FFFF:0`开始执行程序,`FFFF:0`有一条跳转指令,CPU执行后,转去指向BIOS中的硬件系统的检测和初始化程序
- 将BIOS提供的中断例程的入口地址登记在中断向量表中,因为BIOS的中断例程固化在ROM中,因此仅需要登记入口地址
- 硬件系统的检测和初始化完成后,调用`int 19h`进行操作系统的引导
- DOS启动后,将其提供的中断例程写入内存,并将其地址写入中断向量表

!>BIOS和DOS提供的中断例程,都用`ah`来传递内部子程序的编号

### BIOS中断例程应用

```nasm
;int 10h中断例程设置光标位置
mov ah,2;置光标
mov bh,0;第0页
mov dh,5;行号
mov dl,12;列号
int 10h
```

```nasm
;int 10h中断例程在光标位置显示字符
mov ah,9;在光标显示字符
mov al,'a';字符
mov bl,7;颜色属性
mov bh,0;第0页
mov cx,3;字符重复个数
int 10h
```

### DOS中断例程

`mov ax,4c00h  int 21h`调用第`21h`号中断例程的`4ch`号子程序,功能为程序返回

```nasm
;int 21h在光标位置显示字符串
ds:dx指向字符串;要显示的字符串需要用$作为结束符
mov ah,9;9号子程序
int 21h
```

### 实验13 编写,应用中断例程

1. 编写并安装`int 7ch`中断例程,功能为显示一个用0结束的字符串,中断例程安装在`0:200`处

```nasm
assume cs:code
data segment
    db "welcome to masm!",0
data ends
code segment
main:
    mov dh,10;行号
    mov dl,10;列号
    mov cl,2;颜色
    mov ax,data
    mov ds,ax
    mov si,0
    int 7ch
    mov ax,4c00h
    int 21h
code ends
end main
```

```nasm
assume cs:code
code segment
main:
    mov ax,cs
    mov ds,ax
    mov si,offset int7c
    
    mov ax,0000h
    mov es,ax
    mov di,0200h

    cld
    mov cx,offset int7c_end-offset int7c
    rep movsb

    mov ax,0000h
    mov es,ax
    mov word ptr es:[7ch*4],0200h
    mov word ptr es:[7ch*4+2],0000h

    mov ax,4c00h
    int 21h
int7c:
    mov ax,0b800h
    mov es,ax

    mov ax,160
    mul dh
    xor dh,dh
    add dl,dl
    add ax,dx
    mov di,ax;计算偏移
print:
    mov ch,byte ptr ds:[si]
    cmp ch,0
    je finish
    mov byte ptr es:[di],ch
    mov byte ptr es:[di+1],cl
    inc si
    add di,2
    jmp print
finish:
    iret
int7c_end:
    nop
code ends
end main
```

![](https://cdn.jsdelivr.net/gh/followmerushb/followmerushb.github.io@master/static/subject/汇编语言/实验13_1.png)

2. 编写并安装`int 7ch`中断例程,功能为完成loop指令的功能

```nasm
assume cs:code
code segment
main:
    mov ax,0b800h
    mov es,ax
    mov di,160*12
    mov bx,offset s-offset se
    mov cx,80
s:
    mov byte ptr es:[di],'!'
    add di,2
    int 7ch
se:
    nop
    mov ax,4c00h
    int 21h
code ends
end main
```

```nasm
assume cs:code
code segment
main:
    mov ax,cs
    mov ds,ax
    mov si,offset int7c
    
    mov ax,0000h
    mov es,ax
    mov di,0200h

    cld
    mov cx,offset int7c_end-offset int7c
    rep movsb

    mov ax,0000h
    mov es,ax
    mov word ptr es:[7ch*4],0200h
    mov word ptr es:[7ch*4+2],0000h

    mov ax,4c00h
    int 21h
int7c:
    push bp
    mov bp,sp
    dec cx
    jcxz finish
    add ss:[bp+2],bx
finish:
    mov sp,bp
    pop bp
    iret
int7c_end:
    nop
code ends
end main
```

![](https://cdn.jsdelivr.net/gh/followmerushb/followmerushb.github.io@master/static/subject/汇编语言/实验13_2.png)