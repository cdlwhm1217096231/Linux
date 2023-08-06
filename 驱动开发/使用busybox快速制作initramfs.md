*   1.在home目录下创建initramfs/bin文件夹
*   2.从已经编译并安装好的busybox目录/home/cdl/sourceLinuxCode/busybox-1.35.0/\_install/bin/下将可执行程序busybox拷贝到initramfs/bin目录下
*   3.在initramfs目录下创建文件init

```shell
#!/bin/busybox sh

/bin/busybox mkdir -p /proc && /bin/busybox mount -t proc none /proc

/bin/busybox echo "Hello Linux"

export 'PS1=(kernel) =>'
/bin/busybox sh

```

*   4.切换到目录initramfs下，执行find . -print0 | cpio -ov --null --format=newc | gzip -9 > initramfs.img命令将该目录下的所有文件使用cpio命令归档并使用gzip进行压缩，生成initramfs.img文件

