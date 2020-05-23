### 技术交流QQ群:1027579432，欢迎你的加入！

#### 1.make介绍
- gcc：编译器(**gcc根据菜谱进行编译**)
- make: linux自带的构建器(**相当于一个菜谱**)
    - 构建的规则(**菜谱**)在makefile中

#### 2.makefile文件的命名
- makefile
- Makefile

#### 3.makefile中的规则
- gcc a.c b.c c.c -o app这种方法在文件很多情况下，利用gcc编译时会有很多参数，不利于整个项目的管理。因此，需要编写makefile文件，便于整个项目的编译。
- makefile中的规则组成：目标，依赖，命令，具体格式如下：
    ```
    目标:依赖
    (tab缩进) 命令
    ```
- 将gcc a.c b.c c.c -o app修改为make进行编译。于是，makefile中的规则如下所示：
    ```
    app:a.c b.c c.c
        gcc a.c b.c c.c -o app
    ```
- **makefile中由一条或多条规则组成**！

#### 4.makefile的编写
- 具体实例如下所示，**第一个版本**：
    ```
    // gcc编译
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/Makefile$ ls
    add.c  head.h  main.c  mul.c  sub.c
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/Makefile$ gcc add.c main.c sub.c -o app
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/Makefile$ ./app
    sum=36
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/Makefile$ rm app

    // make编译
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/Makefile$ vim makefile
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/Makefile$ cat makefile 
    app:add.c main.c sub.c
        gcc add.c main.c sub.c -o app
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/Makefile$ ls
    add.c  head.h  main.c  makefile  mul.c  sub.c
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/Makefile$ make
    gcc add.c main.c sub.c -o app
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/Makefile$ ./app
    sum=36
    ```
- 第一个版本的缺点：**编译步骤花费时间很长，效率低，修改其中一个文件，所有文件会被重新编译**。
- 具体实例如下所示，**第二个版本**：
    ```
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/Makefile$ vim makefile 
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/Makefile$ cat makefile 
    app:add.o main.o sub.o
        gcc add.o main.o sub.o -o app

    add.o:add.c
        gcc add.c -c

    main.o:main.c
        gcc main.c -c

    sub.o:sub.c
        gcc sub.c -c
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/Makefile$ rm app
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/Makefile$ make
    gcc add.c -c
    gcc main.c -c
    gcc sub.c -c
    gcc add.o main.o sub.o -o app
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/Makefile$ ./app
    sum=36
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/Makefile$ vim add.c  # 仅对add.c文件增加一个空行！
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/Makefile$ make
    gcc add.c -c
    gcc add.o main.o sub.o -o app
    ```
- makefile工作原理：
    - 检测依赖(**如第二个版本中的add.o main.o sub.o**)是否存在：
        - 向下搜索下面的规则(**如第二个版本中的add.o:add.c gcc add.c -c**)，如果有规则是用来生成查找的依赖(**如第二个版本中的add.o main.o sub.o**)的,执行规则中的命令；
    - 依赖存在，判断是否需要更新：
        - 原则：目标的时间 > 依赖的时间。反之，则更新。(**如第二个版本中vim add.c，仅对add.c文件增加一个空行！make时只对add.c进行编译**)
- 第二个版本的缺点：**makefile文件中有很多内容是冗余的**。
- 具体实例如下所示，**第三个版本**：
    - 自定义变量：obj=a.o b.o c.0
        - 变量的取值：aa=$(obj)
    - makefile自带的变量：大写
        - CPPFLAGS
        - CC
    - 自动变量：自动变量只能在**规则的命令**中使用！
        - $@：规则中的目标
        - $<：规则中的第一个依赖
        - $^：规则中所有的依赖
    ```
    dl@cdl-Inspiron-5421:~/Cpp_Tutorials/Makefile$ vim makefile 
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/Makefile$ cat makefile 
    obj = add.o main.o sub.o
    target = app
    $(target):$(obj)
        gcc $(obj) -o $(target)
    # 也可以使用自动变量
        gcc $^ -o $@

    %.o:%.c
        gcc -c $< -o $@ 
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/Makefile$ rm *.o
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/Makefile$ make
    cc    -c -o add.o add.c
    cc    -c -o main.o main.c
    cc    -c -o sub.o sub.c
    gcc add.o main.o sub.o -o app
    ```
- **模式匹配**：%.o:%.c
    - 第一次：add.o没有
    ```
    add.o:add.c
        gcc -c add.c -o add.o
    ```
    - 第二次:main.o没有
    ```
    main.o:main.c
        gcc -c main.c -o main.o
    ```

#### 5.makefile中的函数
- 第三个版本的缺点：**可移植性比较差**。
- 具体实例如下所示，**第四个版本**：
    - makefile中的所有函数都是有返回值
    - wildcard查找指定目录下的指定类型的文件(如 ./*.c)：src = $(wildcard ./*.c)
    - 匹配替换：obj = $(patsubst %.c, %.o, $(src))
    ```
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/Makefile$ vim makefile 
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/Makefile$ cat makefile 
    src = $(wildcard ./*.c)
    obj = $(patsubst %..c, %..o, $(src))
    target = app
    $(target):$(obj)
        gcc $^ -o $@

    %..o:%..c
        gcc -c $< -o $@ 
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/Makefile$ make
    gcc mul.c main.c add.c sub.c -o app
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/Makefile$ ./app
    sum=36
    ```

#### 6.makefile中添加项目清理功能
- 第四个版本的缺点：**不能清理项目**。
- 具体实例如下所示，**第五个版本**：
    - 让make生成不是终极目标的目标：make 目标名
    - 编写一个清理项目的规则：
    ```
    clean:
        rm *.o app
    ```
    - 声明伪目标：
    ```
    .PHONY:clean
    ```
    - -f：强制执行
    - 命令前加-：忽略执行失败的命令，继续向下执行其余的命令；
    ```
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/Makefile$ cat makefile 
    src = $(wildcard ./*.c)
    obj = $(patsubst %..c, %..o, $(src))
    target = app
    $(target):$(obj)
        gcc $^ -o $@

    %..o:%..c
        gcc -c $< -o $@ 
    .PHONY:clean
    clean:
        -mkdir /abc 
        -rm  $(obj) $(target) -f
    ```