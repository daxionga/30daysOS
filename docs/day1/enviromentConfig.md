# 环境配置  
作者已经给qemu这一套弄到docker里了，我们用他的就行  
## 1.Repository及配置  
**[docker环境qemu](https://github.com/HariboteOS/tolenv)**  
这哥们把编译器据说是重新写了一遍具体没往后看，但是用的东西都比较特别，所以说，就照着他的来吧。  
配置环境这一块比较简单,就是拉下来镜像然后run就行，不过看了下人家孩子写的makefile，他用的pwd去映射的路径，这个在makefile里很合理，但是如果要改写脚本的话，建议把
```make
$$(pwd)
```
改成
```bash
$(pwd)/$(dirname $0)
```  
## 验证配置没问题  
这个完全都是用人家的东西要是还能出问题，其实就不太适合看这本书了，但是为了完整性，还是给这个加一个验证环节。首先复制下面的代码，到容器里  

</br></br>
<p style=color:red>todo:有些东西还没弄明白，在代码注释里，到时候以后明白了给注释补上,另外还得测试那个磁盘名等那些字符串是由于固定了字段长度不让改,还是由于他前面写了jmp到一个固定地址不然改</p>    
<p><font color=#ffff00>todo:objdmp反汇编这个得学,online只是临时的链接而且也不全</font></p>

[x86/64在线查反汇编](https://defuse.ca/online-x86-assembler.htm)

</br><br>

```x86asm
;hello-os
;TAB=4
DB 0xeb,0x4e ;这个其实是jmp 0x50的机器码.0x4e=0x50-0x2
DB 0x90      ;就是nop
;FAT12格式的文件系统写法，固定,接下来用点不标准的英语，中英文切换太麻烦
DB "HELLOIPL" ;any string but  must 8 bytes because jmp 0x50 is fixed
DW 512        ;sector size
DB 1          ;cluster szie
DW 1          ;FAT start pos (sector 1 normally)
DB 2          ;I dont know why                                          need to modify comment
DW 224        ;you can push PR to add these two line comment            need to modify comment
DW 2880       ;disk size in KB
DB 0xf0       ;disk type
DW 9          ;                                                         need to add comment
DW 18         ;sector size with 1 track
DW 2          ;magnetic head size
DD 0          ;partition size (no partition in this case)
DD 2880       ;                                                         need to add comment
DB 0,0,0x29   ;nothing from book                                        need to modify commnet
DD 0xffffffff ;the book use word "maybe" to introduce this field(boom!) need to modify comment
DB "HELLO-OS   " ;disk name ,  must 11 bytes  because of 0x50 too
DB "FAT12   " ;file system Name ,8byte
RESB 18       ;blank 18 bytes with 0x00

;code that need be ran

DB 0xb8,0x00,0x00,0x8e,0xd0,0xbc,0x00,0x7c  ;这玩意是16位指令上面链接没有这相关的指令(boom!)   need to modify comment
DB 0x8e,0xd8,0x8e,0xc0,0xbe,0x74,0x7c,0x8a
DB 0x04,0x83,0xc6,0x01,0x3c,0x00,0x74,0x09
DB 0xb4,0x0e,0xbb,0x0f,0x00,0xcd,0x10,0xeb
DB 0xee,0xf4,0xeb,0xfd

;msg
DB 0x0a,0x0a
DB "hello world"
DB 0x0a
DB 0

;rest part with IPL
RESB 0x1fe-$
DB 0x55,0xaa

DB 0xf0,0xff,0xff,0x00,0x00,0x00,0x00,0x00
RESB 4600
DB 0xf0,0xff,0xff,0x00,0x00,0x00,0x00,0x00
RESB 1469432

```
其实还有qemu的配置,我现在是起另外ubuntu容器来运行qemu

<p style=color:red>todo:改天我给他这个容器整理逆向一个dockerfile,把qemu也合到一个容器里</p>
<p style=color:green>环境不是主要问题,这本书内容才是,我打算切回windows进行开发学习了</p>