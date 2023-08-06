*   1.计算机的启动过程
    *   主板上电
    *   CPU reset
    *   Fireware: BIOS/UEFI
    *   BootLoader\:GRUB/FIFO
    *   Kernel
    *   init进程
    *   shell/login

*   2.运行内核前置四件套

    *   (1).编译内核
        *   make mrproper
        *   make menuconfig
        *   make -j4
    *   (2).编译busybox:基础的命令行工具sh cat echo等
        *   make menuconfig
        *   make -j4
    *   (3).制作initramfs,为什么需要initramfs?第一个进程init存储在根文件系统或内存文件系统中
        *   制作方式\:busybox打包为cpio，直接编译进内核make menuconfig
        *   具体制作方法:
            *   find . -print0 | cpio -ov --null --format=newc | gzip -9 > ../initramfs.img
            *   命令详细解释:三个命令组合,最后是一个输出重定向命令
            *   find 查找文件命令
            *   cpio打包归档
                *   null不使用文件名
                *   o创建
                *   v输出详细信息
                *   format打包的文件格式,包括tar newc odc等,newc支持超过65536个inode的文件系统
                *   gzip压缩，9是压缩级别最慢但压缩体积最小,默认是6
    *   (4).启动QEMU
        *   qemu-system-x86\_64
        *   软件模拟硬件,运行编译后的内核

    ```c
    qemu-system-x86_64 \
    -kernel bzImage \
    -initrd initramfs.img \
    -m 1G \
    -nographic \
    -append "earlyprintk=serial,ttyS0 console=ttyS0"
    ```

    *   (5).退出QEMU

        *   Ctrl+A,然后按X

