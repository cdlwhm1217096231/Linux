#### 1.准备条件

*   内核镜像文件\:bzImage(X86\_64)
*   GDB\:X86\_64版本
*   QEMU\:qemu-system-x86\_64
*   可参考往期文章xxx获取内核编译、通过busybox制作initramfs、QEMU运行内核方法

#### 2.编译内核

*   编译时开启内核调试选项\:CONFIG\_DEBUG\_INFO
*   结果文件\:vmlinux(ELF格式文件)、System.map(符号映射表)，每次编译完内核后vmlinux和System.map文件都会重新生成

#### 3.QEMU和GDB调试Linux内核

*   QEMU调试参数
    *   cmdline\:nokaslr(禁止内核地址空间布局随机)
    *   \-S:在开始时阻塞CPU执行
    *   \-s:开启GDB服务器，端口号1234
    *   \-gdb tcp::1234开启GDB服务器，端口号也可以自己指定
*   gdb vmlinux
    *   target remote:1234
    *   break start\_kernel
    *   continue
    *   step

```Makefile
cpimage:
	cp ~/sourceLinuxCode/linux-5.19.4/arch/x86/boot/bzImage ./bzImage

initramfs:
	cd  ./initramfs_dir && find . -print0 | cpio -ov --null --format=newc | gzip -9 > ../initramfs.img

run:
	qemu-system-x86_64  \
		-kernel bzImage \
		-initrd initramfs.img \
		-m 256M \
		-nographic \
		-append "earlyprintk=serial,ttyS0 console=ttyS0 nokaslr" \
		-S \
		-gdb tcp::8080
```

#### 4.gdbinit

*   为了减少类似gdb vmlinux、target remote :8080、手工设置断点等这些重复的操作，可以在内核源码的顶层文件夹下创建文件.gdbinit

    target remote :8080
    break start\_kernel
    continue

*   为了解决gdb vmlinux过程中出现的warning问题，需要在home路径下创建.gdbinit文件并写入如下内容:

    add-auto-load-safe-path /home/cdl/sourceLinuxCode/linux-5.19.4/.gdbinit
    add-auto-load-safe-path /home/cdl/sourceLinuxCode/linux-5.19.4/scripts/gdb/vmlinux-gdb.py

