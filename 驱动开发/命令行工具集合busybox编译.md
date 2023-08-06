*   1.busyBox源码下载:<https://busybox.net/downloads/>
*   2.解压源码后，进行make menuconfig配置编译选项。开启静态编译Build Static binary(no shared libs)选项
*   3.编译busyBox源码\:make -j4
*   4.编译完成后进行安装\:make install
*   5.命令行工具安装在路径:/home/cdl/sourceLinuxCode/busybox-1.35.0/\_install/bin/
*   6.在命令行工具安装路径下执行可执行程序./busybox --help可以查询当前所有可用的命令
*   7.在命令行工具安装路径下执行可执行程序./busybox sh可以进入shell环境

