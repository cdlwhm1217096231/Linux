#### 1.计算机启动过程
- 按下电源，主板上电
- CPU reset
- Fireware:BIOS/UEFI
- Bootloader:GRUB/FIFO
    - 解压内核镜像文件bzImage
    - 将内核加载进内存
    - 将initramfs加载进内存
- kernel
    - mount ramfs/tmpfs(内存上的文件系统)
        - 源码实现fs/ramfs/inode.c
        - 查看注册的文件系统 cat /proc/filesystems | grep ramfs
        - 解压initramfs到rootfs
        - 找到init程序,将控制权交给init
    - init进程完成其他事情,并挂载真实的rootfs

#### 2.ramdisk/ramfs/tmpfs/rootfs
- ramdisk
    - 内存上划出的一块区域/dev/initrd
    - 需要依赖于块设备,块设备是固定大小的
    - 需要文件系统去识别数据==>内核中要有对应的驱动
    
- ramfs
    - 据说是Linus Torvalds实现的
    - 在内存上挂载文件系统

- tmpfs
    - ramfs的升级版
    - 可以使用交换空间,内存满的时候仍然可以正常运行

- rootfs(/)
    - 是ramfs和tmpfs的一个实例
    - 区别于/root,/root是root用户的home目录

#### 3.initrd
- initial RAM disk
    - 块设备:/dev/initrd
    - 已经被initramfs取代
    - 要求内核有对应的文件系统驱动
    - 大小是固定的,不可以动态调整
- man initrd
- 目的
    - 内核中保留少量的启动代码,将需要加载的模块等放在initrd中,实现精简内核代码
- 不足
    - 基于块设备且大小固定
        - 太小会导致init脚本放不下
        - 太大会浪费内存空间

#### 4.initramfs
- initial RAM filesystem
- 2.6及以后的内核/boot/initramfs.img
- 格式:gzip压缩的cpio包
- 创建:
    - 使用cpio打包
    - 使用gzip压缩cpio包
- 使用方式:
    - 在系统引导时,cpio包会被解压,里面的文件会被加载进内存
    - 文件系统在使用前必须挂载

#### 5.initramfs生成方式
- cpio && gzip
    - ls | cpio -ov -H newc | gzip > ./initramfs.img
- dracut
    - 生成的文件为initrd(实际上是initramfs)
- 编译进内核
    - 如果内核中initramfs和外部的initramfs都存在,则外部的会覆盖内核中的

#### 6.解压一个真实的initramfs
- /boot/initramfs.img
    - 本质:经过gzip压缩的cpio包
- 拆解
    - 解压
        - mv initramfs.img initramfs.img.gz
        - gunzip initramfs.img.gz
    - 解包
        - mkdir -p initramfs
        - cpio -i -D initramfs < initramfs.img
    - init
        - modprob挂载模块
        - switch_root/new_root/sbin/init切换到真实的rootfs
        
#### 7.initramfs VS initrd
- 相同点
    - 目标:在引导阶段,加载真实的文件系统
    - 将一些内核不方便做的事情放到用户态(init进),例如挂载文件系统、加载模块  
- 不同基础
    - initrd基于块设备,需要内核有文件系统驱动,大小固定
    - initramfs基于文件,多个文件打包,大小可伸缩
- 创建方式不同
    - initrd需要内核有文件系统驱动
    - initramfs有压缩包即可