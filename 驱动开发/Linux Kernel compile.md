*   1.内核源码下载:<http://ftp.sjtu.edu.cn/sites/ftp.kernel.org/pub/linux/kernel/v5.x/>
*   2.查看编译机器参数命令\:screenfetch
*   3.三种编译方式进行内核编译配置:
    *   make menuconfig
    *   make xconfig
    *   make gconfig(需要安装GTK Package)
*   4.查看内核配置文件: ll -a | grep config
*   5.清除上一次构建生成的config文件\:make mrproper
*   6.编译内核\:make -jn 可以加快内核编译，n为线程数
*   7.清除上一次内核编译过程生成的文件\:make clean
*   8.内核编译结果路径:/home/cdl/sourceLinuxCode/linux-5.19.4/arch/x86/boot/bzImage

