### [BX]

`[0]`表示内存单元,偏移地址为0,`[BX]`同样表示内存单元,偏移地址为`BX`寄存器中的内容

![](https://cdn.jsdelivr.net/gh/followmerushb/followmerushb.github.io@master/static/subject/汇编语言/bx.png)

!>必须是基地址寄存器bx,不能是其它寄存器,如ax等,都会报错

!>约定使用`idata`表示常量,`mov bx,idata`代表如`mov bx,1`

!>在DOS方式下,通常使用`0:200~0:2ff`的空间

### loop

```nasm
assume cs:code
code segment

    mov ax,2
    mov cx,11
s:  add ax,ax
    loop s

    mov ax,4c00h
    int 21h

code ends
end
```

1. 标号

在汇编语言中,标号代表一个地址,标号`s`标识了一个地址,这个地址有一条指令`add ax,ax`

2. `loop s`

CPU执行`loop s`时,进行以下两步操作

- `cx=cx-1`
- 判断`cx`中的值,不为0则跳转到标号s所标识的地址处执行,如果为0则执行下一条指令

```nasm
mov cx,循环次数
s:循环执行的程序段
loop s
```

```nasm
assume cs:codesg
codesg segment

    mov ax,0ffffh
    mov ds,ax
    mov ax,0
    mov al,byte ptr ds:[06h];mov bx,6  mov al,[bx]  mov ah,0
    mov dx,0

    mov cx,3
s:  add dx,ax
    loop s

    mov ax,4c00h
    int 21h

codesg ends
end
```

!>在汇编源程序中,数据不能以字母开头,`A000h`要在前面加0,即`0A000h`

!>在汇编源程序中,不能直接写`mov ax,[0]`,而是`mov bx,0  mov ax,[bx]`或者`mov ax,ds:[0]`

?>计算`FFFF:0~FFFF:B`单元中的数据的和

```nasm
assume cs:codesg
codesg segment

    mov ax,0ffffh
    mov ds,ax
    mov ax,0
    mov bx,0
    mov dx,0

    mov cx,0ch
s:  mov al,[bx]
    inc bx
    add dx,ax
    loop s

    mov ax,4c00h
    int 21h

codesg ends
end
```

### 段前缀

```nasm
mov ax,ds:[bx];段地址在ds中
mov ax,cs:[bx];段地址在cs中
mov ax,ss:[bx];段地址在ss中
mov ax,es:[bx];段地址在es中
```

`ds`,`cs`,`ss`,`es`称为**段前缀**

`ES`附加段寄存器可以作为`DS`,当`DS`不够用时可以用`ES`作为补充

---

将内存`ffff:0~ffff:b`单元中的数据复制到`0:200~0:20b`单元中

`0:200~0:20b`等价于`0020:0~0020:b`

```nasm
assume cs:codesg
codesg segment

    mov ax,0ffffh
    mov ds,ax
    mov ax,0020h
    mov es,ax
    mov ax,0
    mov bx,0
    mov cx,0ch
    
    copy:
    mov al,ds:[bx]
    mov es:[bx],al
    inc bx
    loop copy

    mov ax,4c00h
    int 21h

codesg ends
end
```

### 实验4 [bx]和loop的使用

编程,向内存`0:200~0:23f`依次传送数据`0~3fh`

```nasm
assume cs:codesg
codesg segment

    mov ax,0020h
    mov ds,ax
    mov bx,0
    mov cx,40h

    copy:
    mov ds:[bx],bl
    inc bl
    loop copy

    mov ax,4c00h
    int 21h

codesg ends
end
```

![](https://cdn.jsdelivr.net/gh/followmerushb/followmerushb.github.io@master/static/subject/%E6%B1%87%E7%BC%96%E8%AF%AD%E8%A8%80/%E5%AE%9E%E9%AA%8C4.png)