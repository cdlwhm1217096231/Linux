### 技术交流QQ群:1027579432，欢迎你的加入！
#### 1.gcc工作流程
- 预处理：--E
    - 宏替换
    - 头文件展开
    - 注释去掉
    - xxx.c文件变成xxx.i文件(实际上也是c文件)
- 编译(此步骤时间最长)：--S
    - xxx.i文件变成xxx.s文件(**汇编文件**)
- 汇编：-c
    - xxx.s文件变成xxx.o文件(**二进制文件**)
- 链接：
    - xxx.o文件变成xxx文件(**可执行**)
![gcc工作流程.png](https://upload-images.jianshu.io/upload_images/13407176-ebd832c1483c7a5b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- gcc hello.c：默认编译生成的可执行文件名为a.out；
- gcc hello.c -o hello:指定编译生成的可执行文件名为hello；
- 只有编译步骤是gcc完成的，其余步骤都是gcc调用其他工具(如预处理器、链接器等)实现的
#### 2.gcc常用参数
- -v/--version: 查看gcc版本
- **-I**:编译的时候，指定头文件的路径。例如:gcc sum.c -I ./include/ -o sum
- **-c**:将汇编文件生成二进制文件,得到了一个.o文件。例如:gcc sum.c -c -I ./include/
- **-o**:指定生成的文件的名字。例如：gcc sum.c -c -I ./include/ -o aa.o
- -g:gbd调试的时候需要增加的参数。例如:gcc hello.c -o app1 -g
- -D:在编译的时候指定一个宏，使用场景：测试程序的时候使用。例如：gcc sum.c -I ./include/ -D DEBUG -o app
- -Wall:添加警告信息。例如：gcc sum.c -I ./include/ -D DEBUG -o app1 -Wall
- -On:优化代码，n是优化级别：1,2,3。
