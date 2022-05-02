# Makfile入门相关

创建一个叫Makefile的文件在文件中写入：

```x86asm
ipl.bin : ipl.nas Makefile
	../z_tools/nask.exe ipl.nas ipl.bin ipl.lst
	
helloos.img :  ipl.bin Makefile
	../z_tools/edimg.exe	imgin:../z_tools/fdimg0at.tek ¥
		wbingimg src:ipl.bin len:512 from:0 to:0	imgout:helloos.img

img :
	../z_tools/makes.exe/make.exe -r helloos.img
	
asm :
	../z_tools/make.exe -r ipl.bin
	
run : 
	../z_tools/make.exe img
	copy helloos.img .. ¥z_tools¥qemu¥fdimage0.bin
	../z_tools/make.exe -C ../z_tools/qemu
	
install : 
	../z_tools/make.exe img
	../z_tools/imgtol.com w a: helloos.img
	
clean :
	-del ipl.bin
	-del ipl.lst	
```



