### 技术交流QQ群:1027579432，欢迎你的加入！

#### 1.gdb调试
- gcc a.c b.c c.c -o app：无法进行gbd调试
- gcc a.c b.c c.c -o app -g：可以进行gdb调试
    - -g：会保留函数名和变量名

#### 2.启动gdb
- 启动方法：gdb 可执行程序的名字，例如gbd app
- 给程序传参：set args xxx  xxx，如下例所示：
    ```
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/GDB/args$ cat test.c
    /************************************************************************
        > File Name: test.c
        > Author: CurryCoder
        > Mail: 1217096231@qq.com 
        > Created Time: 2020年05月23日 星期六 10时55分00秒
    ************************************************************************/

    #include<stdio.h>

    int main(int argc, const char* argv[]){
        printf("args num = %d\n", argc);
        for(int i = 0; i < argc; i++){
            printf("arg%d: %s\n", i, argv[i]);
        }
        return 0;
    }
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/GDB/args$ gcc test.c 
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/GDB/args$ 
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/GDB/args$ ls
    a.out  test.c
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/GDB/args$ ./a.out
    args num = 1
    arg0: ./a.out
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/GDB/args$ ./a.out abc def gh 000 666
    args num = 6
    arg0: ./a.out
    arg1: abc
    arg2: def
    arg3: gh
    arg4: 000
    arg5: 666

    /********************************************使用gbd开始进行调试*********************************/

    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/GDB/args$ gcc test.c -g -o app
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/GDB/args$ gdb app 
    GNU gdb (Ubuntu 7.11.1-0ubuntu1~16.5) 7.11.1
    Copyright (C) 2016 Free Software Foundation, Inc.
    License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
    This is free software: you are free to change and redistribute it.
    There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
    and "show warranty" for details.
    This GDB was configured as "x86_64-linux-gnu".
    Type "show configuration" for configuration details.
    For bug reporting instructions, please see:
    <http://www.gnu.org/software/gdb/bugs/>.
    Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.
    For help, type "help".
    Type "apropos word" to search for commands related to "word"...
    Reading symbols from app...done.
    (gdb) set args hello world 666
    (gdb) r
    Starting program: /home/cdl/Cpp_Tutorials/GDB/args/app hello world 666
    args num = 4
    arg0: /home/cdl/Cpp_Tutorials/GDB/args/app
    arg1: hello
    arg2: world
    arg3: 666
    [Inferior 1 (process 30054) exited normally]
    (gdb) Quit
    ```

#### 3.查看代码:list
    - 当前文件：
        - l
        - l 行号
        - l 函数名
    - 非当前文件：
        - l 文件名:行号
        - l 文件名:函数名
    - 设置显示的行数：
        - set listsize n
        - show listsize
    ```
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/GDB$ ls
    app  args  insert_sort.c  main.c  makefile  mysort  select_sort.c  sort  sort.h
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/GDB$ 
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/GDB$ 
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/GDB$ gdb app
    GNU gdb (Ubuntu 7.11.1-0ubuntu1~16.5) 7.11.1
    Copyright (C) 2016 Free Software Foundation, Inc.
    License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
    This is free software: you are free to change and redistribute it.
    There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
    and "show warranty" for details.
    This GDB was configured as "x86_64-linux-gnu".
    Type "show configuration" for configuration details.
    For bug reporting instructions, please see:
    <http://www.gnu.org/software/gdb/bugs/>.
    Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.
    For help, type "help".
    Type "apropos word" to search for commands related to "word"...
    Reading symbols from app...done.
    (gdb) l
    1	#include <stdio.h>
    2	#include "sort.h"
    3	
    4	void main()
    5	{
    6		int i;
    7		//������������
    8		int array[] = { 12, 5, 33, 6, 10, 35, 67, 89, 87, 65, 54, 24, 58, 92, 100, 24, 46, 78, 99, 200, 55, 44, 33, 22, 11, 71, 2, 4, 86, 8, 9 };
    9		int array2[] = { 12, 5, 33, 6, 10, 35, 67, 89, 87, 65, 54, 24, 58, 92, 100, 24, 46, 78, 99, 200, 55, 44, 33, 22, 11, 71, 2, 4, 86, 8, 9 };
    10	
    (gdb) show listsize 5
    Number of source lines gdb will list by default is 10.
    (gdb) l
    11		//�������鳤��
    12		int len = sizeof(array) / sizeof(int);
    13		//��������
    14		printf("Sort Array:\n");
    15		for (i = 0; i < len; ++i)
    16		{
    17			printf("%d\t", array[i]);
    18		}
    19		printf("\n");
    20	
    (gdb) set listsize 5
    (gdb) l 5
    3	
    4	void main()
    5	{
    6		int i;
    7		//������������
    (gdb) Quit
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/GDB$ 
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/GDB$ 
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/GDB$ gdb app
    GNU gdb (Ubuntu 7.11.1-0ubuntu1~16.5) 7.11.1
    Copyright (C) 2016 Free Software Foundation, Inc.
    License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
    This is free software: you are free to change and redistribute it.
    There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
    and "show warranty" for details.
    This GDB was configured as "x86_64-linux-gnu".
    Type "show configuration" for configuration details.
    For bug reporting instructions, please see:
    <http://www.gnu.org/software/gdb/bugs/>.
    Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.
    For help, type "help".
    Type "apropos word" to search for commands related to "word"...
    Reading symbols from app...done.
    (gdb) l
    1	#include <stdio.h>
    2	#include "sort.h"
    3	
    4	void main()
    5	{
    6		int i;
    7		//������������
    8		int array[] = { 12, 5, 33, 6, 10, 35, 67, 89, 87, 65, 54, 24, 58, 92, 100, 24, 46, 78, 99, 200, 55, 44, 33, 22, 11, 71, 2, 4, 86, 8, 9 };
    9		int array2[] = { 12, 5, 33, 6, 10, 35, 67, 89, 87, 65, 54, 24, 58, 92, 100, 24, 46, 78, 99, 200, 55, 44, 33, 22, 11, 71, 2, 4, 86, 8, 9 };
    10	
    (gdb) show listsize
    Number of source lines gdb will list by default is 10.
    (gdb) set listsize 20
    (gdb) l
    11		//�������鳤��
    12		int len = sizeof(array) / sizeof(int);
    13		//��������
    14		printf("Sort Array:\n");
    15		for (i = 0; i < len; ++i)
    16		{
    17			printf("%d\t", array[i]);
    18		}
    19		printf("\n");
    20	
    21		// selectionSort
    22		selectionSort(array, len);
    23		// printf
    24		printf("Selection Sort:\n");
    25		for (i = 0; i < len; ++i)
    26		{
    27			printf("%d  ", array[i]);
    28		}
    29		
    30		// insertionSort
    (gdb) l 5
    1	#include <stdio.h>
    2	#include "sort.h"
    3	
    4	void main()
    5	{
    6		int i;
    7		//������������
    8		int array[] = { 12, 5, 33, 6, 10, 35, 67, 89, 87, 65, 54, 24, 58, 92, 100, 24, 46, 78, 99, 200, 55, 44, 33, 22, 11, 71, 2, 4, 86, 8, 9 };
    9		int array2[] = { 12, 5, 33, 6, 10, 35, 67, 89, 87, 65, 54, 24, 58, 92, 100, 24, 46, 78, 99, 200, 55, 44, 33, 22, 11, 71, 2, 4, 86, 8, 9 };
    10	
    11		//�������鳤��
    12		int len = sizeof(array) / sizeof(int);
    13		//��������
    14		printf("Sort Array:\n");
    15		for (i = 0; i < len; ++i)
    16		{
    17			printf("%d\t", array[i]);
    18		}
    19		printf("\n");
    20	
    (gdb) set listsize 10
    (gdb) 
    (gdb) 
    (gdb) l 5
    1	#include <stdio.h>
    2	#include "sort.h"
    3	
    4	void main()
    5	{
    6		int i;
    7		//������������
    8		int array[] = { 12, 5, 33, 6, 10, 35, 67, 89, 87, 65, 54, 24, 58, 92, 100, 24, 46, 78, 99, 200, 55, 44, 33, 22, 11, 71, 2, 4, 86, 8, 9 };
    9		int array2[] = { 12, 5, 33, 6, 10, 35, 67, 89, 87, 65, 54, 24, 58, 92, 100, 24, 46, 78, 99, 200, 55, 44, 33, 22, 11, 71, 2, 4, 86, 8, 9 };
    10	
    (gdb) l main
    1	#include <stdio.h>
    2	#include "sort.h"
    3	
    4	void main()
    5	{
    6		int i;
    7		//������������
    8		int array[] = { 12, 5, 33, 6, 10, 35, 67, 89, 87, 65, 54, 24, 58, 92, 100, 24, 46, 78, 99, 200, 55, 44, 33, 22, 11, 71, 2, 4, 86, 8, 9 };
    9		int array2[] = { 12, 5, 33, 6, 10, 35, 67, 89, 87, 65, 54, 24, 58, 92, 100, 24, 46, 78, 99, 200, 55, 44, 33, 22, 11, 71, 2, 4, 86, 8, 9 };
    10	
    (gdb) l insert_sort.c:15
    10		�ȶ���:�����������ȶ���
    11	
    12	***********************************************/
    13	
    14	//���������㷨(��������)
    15	void insertionSort(int *array, int len)
    16	{
    17		int tmp = 0;	// �洢��׼��
    18		int index = 0;	// �ӵ�λ��
    19		// ������������
    (gdb) l insert_sort.c:insertionSort
    11	
    12	***********************************************/
    13	
    14	//���������㷨(��������)
    15	void insertionSort(int *array, int len)
    16	{
    17		int tmp = 0;	// �洢��׼��
    18		int index = 0;	// �ӵ�λ��
    19		// ������������
    20		for (int i = 1; i < len; ++i)
    (gdb) l main.c:main
    1	#include <stdio.h>
    2	#include "sort.h"
    3	
    4	void main()
    5	{
    6		int i;
    7		//������������
    8		int array[] = { 12, 5, 33, 6, 10, 35, 67, 89, 87, 65, 54, 24, 58, 92, 100, 24, 46, 78, 99, 200, 55, 44, 33, 22, 11, 71, 2, 4, 86, 8, 9 };
    9		int array2[] = { 12, 5, 33, 6, 10, 35, 67, 89, 87, 65, 54, 24, 58, 92, 100, 24, 46, 78, 99, 200, 55, 44, 33, 22, 11, 71, 2, 4, 86, 8, 9 };
    10	
    (gdb) 
    ```

#### 4.断点相关操作:break/b
- 给当前文件设置断点：
    - b 行号
    - b 函数名
- 给非当前文件设置断点：
    - b 文件名:行号
    - b 文件名:函数名
- 查看断点：info(i) b
- 删除断点：d num(**断点具体的编号,根据i b得到**)
    - 删除多个：d num1 num2 或者 d num1-num6
- 设置断点无效：dis num(**断点具体的编号,根据i b得到**)
    - 多个断点无效：dis num1 num2 或者 dis num1-num6
- 设置断点生效：ena num(**断点具体的编号,根据i b得到**)
    - 多个断点生效：ena num1 num2 或者 ena num1-num6
- 设置条件断点：b 行号 if 变量名 == 变量的值
- 重新从程序开头连续执行：r
- 执行到下一个断点停住，如果后面没有断点，程序执行结束：c
- 查看任何东西（变量/函数等）的值：p 变量名/函数名
- 退出gdb：q
    ```
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/GDB$ gdb app
    GNU gdb (Ubuntu 7.11.1-0ubuntu1~16.5) 7.11.1
    Copyright (C) 2016 Free Software Foundation, Inc.
    License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
    This is free software: you are free to change and redistribute it.
    There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
    and "show warranty" for details.
    This GDB was configured as "x86_64-linux-gnu".
    Type "show configuration" for configuration details.
    For bug reporting instructions, please see:
    <http://www.gnu.org/software/gdb/bugs/>.
    Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.
    For help, type "help".
    Type "apropos word" to search for commands related to "word"...
    Reading symbols from app...done.
    (gdb) l
    1	#include <stdio.h>
    2	#include "sort.h"
    3	
    4	void main()
    5	{
    6		int i;
    7		//������������
    8		int array[] = { 12, 5, 33, 6, 10, 35, 67, 89, 87, 65, 54, 24, 58, 92, 100, 24, 46, 78, 99, 200, 55, 44, 33, 22, 11, 71, 2, 4, 86, 8, 9 };
    9		int array2[] = { 12, 5, 33, 6, 10, 35, 67, 89, 87, 65, 54, 24, 58, 92, 100, 24, 46, 78, 99, 200, 55, 44, 33, 22, 11, 71, 2, 4, 86, 8, 9 };
    10	
    (gdb) 
    11		//�������鳤��
    12		int len = sizeof(array) / sizeof(int);
    13		//��������
    14		printf("Sort Array:\n");
    15		for (i = 0; i < len; ++i)
    16		{
    17			printf("%d\t", array[i]);
    18		}
    19		printf("\n");
    20	
    (gdb) 
    21		// selectionSort
    22		selectionSort(array, len);
    23		// printf
    24		printf("Selection Sort:\n");
    25		for (i = 0; i < len; ++i)
    26		{
    27			printf("%d  ", array[i]);
    28		}
    29		
    30		// insertionSort
    (gdb) b 12
    Breakpoint 1 at 0x400934: file main.c, line 12.
    (gdb) b 14
    Breakpoint 2 at 0x40093e: file main.c, line 14.
    (gdb) b 15
    Breakpoint 3 at 0x400948: file main.c, line 15.
    (gdb) b 17
    Breakpoint 4 at 0x400954: file main.c, line 17.
    (gdb) b 19
    Breakpoint 5 at 0x400989: file main.c, line 19.
    (gdb) b 24
    Breakpoint 6 at 0x4009aa: file main.c, line 24.
    (gdb) b 27
    Breakpoint 7 at 0x4009c0: file main.c, line 27.
    (gdb) i b
    Num     Type           Disp Enb Address            What
    1       breakpoint     keep y   0x0000000000400934 in main at main.c:12
    2       breakpoint     keep y   0x000000000040093e in main at main.c:14
    3       breakpoint     keep y   0x0000000000400948 in main at main.c:15
    4       breakpoint     keep y   0x0000000000400954 in main at main.c:17
    5       breakpoint     keep y   0x0000000000400989 in main at main.c:19
    6       breakpoint     keep y   0x00000000004009aa in main at main.c:24
    7       breakpoint     keep y   0x00000000004009c0 in main at main.c:27
    (gdb) d 1
    (gdb) i b
    Num     Type           Disp Enb Address            What
    2       breakpoint     keep y   0x000000000040093e in main at main.c:14
    3       breakpoint     keep y   0x0000000000400948 in main at main.c:15
    4       breakpoint     keep y   0x0000000000400954 in main at main.c:17
    5       breakpoint     keep y   0x0000000000400989 in main at main.c:19
    6       breakpoint     keep y   0x00000000004009aa in main at main.c:24
    7       breakpoint     keep y   0x00000000004009c0 in main at main.c:27
    (gdb) d 2 3
    (gdb) i b
    Num     Type           Disp Enb Address            What
    4       breakpoint     keep y   0x0000000000400954 in main at main.c:17
    5       breakpoint     keep y   0x0000000000400989 in main at main.c:19
    6       breakpoint     keep y   0x00000000004009aa in main at main.c:24
    7       breakpoint     keep y   0x00000000004009c0 in main at main.c:27
    (gdb) d 4-7
    (gdb) i b
    No breakpoints or watchpoints.
    (gdb) l main
    1	#include <stdio.h>
    2	#include "sort.h"
    3	
    4	void main()
    5	{
    6		int i;
    7		//������������
    8		int array[] = { 12, 5, 33, 6, 10, 35, 67, 89, 87, 65, 54, 24, 58, 92, 100, 24, 46, 78, 99, 200, 55, 44, 33, 22, 11, 71, 2, 4, 86, 8, 9 };
    9		int array2[] = { 12, 5, 33, 6, 10, 35, 67, 89, 87, 65, 54, 24, 58, 92, 100, 24, 46, 78, 99, 200, 55, 44, 33, 22, 11, 71, 2, 4, 86, 8, 9 };
    10	
    (gdb) l
    11		//�������鳤��
    12		int len = sizeof(array) / sizeof(int);
    13		//��������
    14		printf("Sort Array:\n");
    15		for (i = 0; i < len; ++i)
    16		{
    17			printf("%d\t", array[i]);
    18		}
    19		printf("\n");
    20	
    (gdb) l
    21		// selectionSort
    22		selectionSort(array, len);
    23		// printf
    24		printf("Selection Sort:\n");
    25		for (i = 0; i < len; ++i)
    26		{
    27			printf("%d  ", array[i]);
    28		}
    29		
    30		// insertionSort
    (gdb) b 17
    Breakpoint 8 at 0x400954: file main.c, line 17.
    (gdb) i b
    Num     Type           Disp Enb Address            What
    8       breakpoint     keep y   0x0000000000400954 in main at main.c:17
    (gdb) dis 8
    (gdb) i b
    Num     Type           Disp Enb Address            What
    8       breakpoint     keep n   0x0000000000400954 in main at main.c:17
    (gdb) ena 8
    (gdb) i b
    Num     Type           Disp Enb Address            What
    8       breakpoint     keep y   0x0000000000400954 in main at main.c:17
    (gdb) r
    Starting program: /home/cdl/Cpp_Tutorials/GDB/app 
    Sort Array:

    Breakpoint 8, main () at main.c:17
    17			printf("%d\t", array[i]);
    (gdb) i b
    Num     Type           Disp Enb Address            What
    8       breakpoint     keep y   0x0000000000400954 in main at main.c:17
        breakpoint already hit 1 time
    (gdb) d 8
    (gdb) b 17 if i==10
    Breakpoint 9 at 0x400954: file main.c, line 17.
    (gdb) c
    Continuing.

    Breakpoint 9, main () at main.c:17
    17			printf("%d\t", array[i]);
    (gdb) p i
    $1 = 10
    (gdb) p main
    $2 = {void ()} 0x4006ff <main>
    (gdb) q
    ```

#### 5.gdb代码调试相关命令
- 让gdb跑起来：start(**运行一行就停止**)或r(**停在第一个断点的位置**)
- 打印变量的值: p 变量名
- 打印变量的类型：ptype 变量名
- 向下单步调试：n(**遇到函数不会进入函数体内部**)或s(**遇到函数会进入函数体内部**)
- 继续运行gdb，停在下一个断点的位置：c
- 跳出函数体：finish,**如果跳不出去，看一下函数体内部的循环中是否有断点，如果有则删掉或设置无效**。
- 退出gdb: q
- 变量的自动显示：display 变量名
- 取消变量的自动显示：undisplay 编号
    - 编号的查询：i display
- 从循环体中直接跳出：until(**循环体中不能有断点**)
- 直接设置变量等于某一个值：set var 变量名=变量的值
    ```
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/GDB$ gdb app
    GNU gdb (Ubuntu 7.11.1-0ubuntu1~16.5) 7.11.1
    Copyright (C) 2016 Free Software Foundation, Inc.
    License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
    This is free software: you are free to change and redistribute it.
    There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
    and "show warranty" for details.
    This GDB was configured as "x86_64-linux-gnu".
    Type "show configuration" for configuration details.
    For bug reporting instructions, please see:
    <http://www.gnu.org/software/gdb/bugs/>.
    Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.
    For help, type "help".
    Type "apropos word" to search for commands related to "word"...
    Reading symbols from app...done.
    (gdb) list
    1	#include <stdio.h>
    2	#include "sort.h"
    3	
    4	void main()
    5	{
    6		int i;
    7		//������������
    8		int array[] = { 12, 5, 33, 6, 10, 35, 67, 89, 87, 65, 54, 24, 58, 92, 100, 24, 46, 78, 99, 200, 55, 44, 33, 22, 11, 71, 2, 4, 86, 8, 9 };
    9		int array2[] = { 12, 5, 33, 6, 10, 35, 67, 89, 87, 65, 54, 24, 58, 92, 100, 24, 46, 78, 99, 200, 55, 44, 33, 22, 11, 71, 2, 4, 86, 8, 9 };
    10	
    (gdb) l
    11		//�������鳤��
    12		int len = sizeof(array) / sizeof(int);
    13		//��������
    14		printf("Sort Array:\n");
    15		for (i = 0; i < len; ++i)
    16		{
    17			printf("%d\t", array[i]);
    18		}
    19		printf("\n");
    20	
    (gdb) 
    21		// selectionSort
    22		selectionSort(array, len);
    23		// printf
    24		printf("Selection Sort:\n");
    25		for (i = 0; i < len; ++i)
    26		{
    27			printf("%d  ", array[i]);
    28		}
    29		
    30		// insertionSort
    (gdb) b 17
    Breakpoint 1 at 0x400954: file main.c, line 17.
    (gdb) b 22
    Breakpoint 2 at 0x400993: file main.c, line 22.
    (gdb) b 27
    Breakpoint 3 at 0x4009c0: file main.c, line 27.
    (gdb) i b
    Num     Type           Disp Enb Address            What
    1       breakpoint     keep y   0x0000000000400954 in main at main.c:17
    2       breakpoint     keep y   0x0000000000400993 in main at main.c:22
    3       breakpoint     keep y   0x00000000004009c0 in main at main.c:27
    (gdb) r 
    Starting program: /home/cdl/Cpp_Tutorials/GDB/app 
    Sort Array:

    Breakpoint 1, main () at main.c:17
    17			printf("%d\t", array[i]);
    (gdb) p i
    $1 = 0
    (gdb) p array[i]
    $2 = 12
    (gdb) p len
    $3 = 31
    (gdb) ptype i
    type = int
    (gdb) ptype array
    type = int [31]
    (gdb) n
    15		for (i = 0; i < len; ++i)
    (gdb) 

    Breakpoint 1, main () at main.c:17
    17			printf("%d\t", array[i]);
    (gdb) 
    15		for (i = 0; i < len; ++i)
    (gdb) 

    Breakpoint 1, main () at main.c:17
    17			printf("%d\t", array[i]);
    (gdb) 
    15		for (i = 0; i < len; ++i)
    (gdb) 

    Breakpoint 1, main () at main.c:17
    17			printf("%d\t", array[i]);
    (gdb) 
    15		for (i = 0; i < len; ++i)
    (gdb) 

    Breakpoint 1, main () at main.c:17
    17			printf("%d\t", array[i]);
    (gdb) 
    15		for (i = 0; i < len; ++i)
    (gdb) p i
    $4 = 4
    (gdb) n

    Breakpoint 1, main () at main.c:17
    17			printf("%d\t", array[i]);
    (gdb) 
    15		for (i = 0; i < len; ++i)
    (gdb) p i
    $5 = 5
    (gdb) display i
    1: i = 5
    (gdb) display array[i]
    2: array[i] = 35
    (gdb) n

    Breakpoint 1, main () at main.c:17
    17			printf("%d\t", array[i]);
    1: i = 6
    2: array[i] = 67
    (gdb) 
    15		for (i = 0; i < len; ++i)
    1: i = 6
    2: array[i] = 67
    (gdb) 

    Breakpoint 1, main () at main.c:17
    17			printf("%d\t", array[i]);
    1: i = 7
    2: array[i] = 89
    (gdb) 
    15		for (i = 0; i < len; ++i)
    1: i = 7
    2: array[i] = 89
    (gdb) 

    Breakpoint 1, main () at main.c:17
    17			printf("%d\t", array[i]);
    1: i = 8
    2: array[i] = 87
    (gdb) 
    15		for (i = 0; i < len; ++i)
    1: i = 8
    2: array[i] = 87
    (gdb) 

    Breakpoint 1, main () at main.c:17
    17			printf("%d\t", array[i]);
    1: i = 9
    2: array[i] = 65
    (gdb) 
    15		for (i = 0; i < len; ++i)
    1: i = 9
    2: array[i] = 65
    (gdb) 

    Breakpoint 1, main () at main.c:17
    17			printf("%d\t", array[i]);
    1: i = 10
    2: array[i] = 54
    (gdb) 
    15		for (i = 0; i < len; ++i)
    1: i = 10
    2: array[i] = 54
    (gdb) 

    Breakpoint 1, main () at main.c:17
    17			printf("%d\t", array[i]);
    1: i = 11
    2: array[i] = 24
    (gdb) 
    15		for (i = 0; i < len; ++i)
    1: i = 11
    2: array[i] = 24
    (gdb) 

    Breakpoint 1, main () at main.c:17
    17			printf("%d\t", array[i]);
    1: i = 12
    2: array[i] = 58
    (gdb) 
    15		for (i = 0; i < len; ++i)
    1: i = 12
    2: array[i] = 58
    (gdb) i display
    Auto-display expressions now in effect:
    Num Enb Expression
    1:   y  i
    2:   y  array[i]
    (gdb) undisplay 1
    (gdb) undisplay 2
    (gdb) n

    Breakpoint 1, main () at main.c:17
    17			printf("%d\t", array[i]);
    (gdb) 
    15		for (i = 0; i < len; ++i)
    (gdb) 

    Breakpoint 1, main () at main.c:17
    17			printf("%d\t", array[i]);
    (gdb) p i
    $6 = 14
    (gdb) i b
    Num     Type           Disp Enb Address            What
    1       breakpoint     keep y   0x0000000000400954 in main at main.c:17
        breakpoint already hit 15 times
    2       breakpoint     keep y   0x0000000000400993 in main at main.c:22
    3       breakpoint     keep y   0x00000000004009c0 in main at main.c:27
    (gdb) dis 1
    (gdb) i b
    Num     Type           Disp Enb Address            What
    1       breakpoint     keep n   0x0000000000400954 in main at main.c:17
        breakpoint already hit 15 times
    2       breakpoint     keep y   0x0000000000400993 in main at main.c:22
    3       breakpoint     keep y   0x00000000004009c0 in main at main.c:27
    (gdb) n
    15		for (i = 0; i < len; ++i)
    (gdb) 
    17			printf("%d\t", array[i]);
    (gdb) 
    15		for (i = 0; i < len; ++i)
    (gdb) c
    Continuing.
    12	5	33	6	10	35	67	89	87	65	54	24	58	92	100	24	46	78	99	200	55	44	33	22	11	71	2	4	86	8	9

    Breakpoint 2, main () at main.c:22
    22		selectionSort(array, len);
    (gdb) p i
    $7 = 31
    (gdb) step
    selectionSort (array=0x7fffffffdbd0, len=31) at select_sort.c:17
    17		int min = 0;	// ָ����С��Ԫ�ص�λ��
    (gdb) l
    12	-------------------------------------------------*/
    13	
    14	//ѡ������(��������)
    15	void selectionSort(int *array, int len)
    16	{
    17		int min = 0;	// ָ����С��Ԫ�ص�λ��
    18		// ����ѭ��
    19		for (int i = 0; i < len - 1; ++i)
    20		{
    21			min = i;
    (gdb) 
    22			// �ڴ�ѭ��
    23			for (int j = i + 1; j < len; ++j)
    24			{
    25				// �ж�
    26				if (array[min] > array[j])
    27				{
    28					// ������С��Ԫ�ص�λ��
    29					min = j;
    30				}
    31			}
    (gdb) 
    32			// �ж��Ƿ���Ҫ����
    33			if (min != i)
    34			{
    35				// �ҵ����µ���Сֵ
    36				// ����
    37				int tmp = array[min];
    38				array[min] = array[i];
    39				array[i] = tmp;
    40			}
    41		}
    (gdb) finish
    Run till exit from #0  selectionSort (array=0x7fffffffdbd0, len=31)
        at select_sort.c:17
    main () at main.c:24
    24		printf("Selection Sort:\n");
    (gdb) q
    A debugging session is active.

        Inferior 1 [process 5911] will be killed.

    Quit anyway? (y or n) y
    cdl@cdl-Inspiron-5421:~/Cpp_Tutorials/GDB$ gdb app
    GNU gdb (Ubuntu 7.11.1-0ubuntu1~16.5) 7.11.1
    Copyright (C) 2016 Free Software Foundation, Inc.
    License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
    This is free software: you are free to change and redistribute it.
    There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
    and "show warranty" for details.
    This GDB was configured as "x86_64-linux-gnu".
    Type "show configuration" for configuration details.
    For bug reporting instructions, please see:
    <http://www.gnu.org/software/gdb/bugs/>.
    Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.
    For help, type "help".
    Type "apropos word" to search for commands related to "word"...
    Reading symbols from app...done.
    (gdb) l
    1	#include <stdio.h>
    2	#include "sort.h"
    3	
    4	void main()
    5	{
    6		int i;
    7		//������������
    8		int array[] = { 12, 5, 33, 6, 10, 35, 67, 89, 87, 65, 54, 24, 58, 92, 100, 24, 46, 78, 99, 200, 55, 44, 33, 22, 11, 71, 2, 4, 86, 8, 9 };
    9		int array2[] = { 12, 5, 33, 6, 10, 35, 67, 89, 87, 65, 54, 24, 58, 92, 100, 24, 46, 78, 99, 200, 55, 44, 33, 22, 11, 71, 2, 4, 86, 8, 9 };
    10	
    (gdb) 
    11		//�������鳤��
    12		int len = sizeof(array) / sizeof(int);
    13		//��������
    14		printf("Sort Array:\n");
    15		for (i = 0; i < len; ++i)
    16		{
    17			printf("%d\t", array[i]);
    18		}
    19		printf("\n");
    20	
    (gdb) 
    21		// selectionSort
    22		selectionSort(array, len);
    23		// printf
    24		printf("Selection Sort:\n");
    25		for (i = 0; i < len; ++i)
    26		{
    27			printf("%d  ", array[i]);
    28		}
    29		
    30		// insertionSort
    (gdb) b 17
    Breakpoint 1 at 0x400954: file main.c, line 17.
    (gdb) r
    Starting program: /home/cdl/Cpp_Tutorials/GDB/app 
    Sort Array:

    Breakpoint 1, main () at main.c:17
    17			printf("%d\t", array[i]);
    (gdb) start
    The program being debugged has been started already.
    Start it from the beginning? (y or n) y
    Temporary breakpoint 2 at 0x40070a: file main.c, line 5.
    Starting program: /home/cdl/Cpp_Tutorials/GDB/app 

    Temporary breakpoint 2, main () at main.c:5
    5	{
    (gdb) c
    Continuing.
    Sort Array:

    Breakpoint 1, main () at main.c:17
    17			printf("%d\t", array[i]);
    (gdb) p i
    $1 = 0
    (gdb) p array[i]
    $2 = 12
    (gdb) set var i=5
    (gdb) p i
    $3 = 5
    (gdb) p array[i]
    $4 = 35
    (gdb) until
    15		for (i = 0; i < len; ++i)
    (gdb) 

    Breakpoint 1, main () at main.c:17
    17			printf("%d\t", array[i]);
    (gdb) 
    15		for (i = 0; i < len; ++i)
    (gdb) 

    Breakpoint 1, main () at main.c:17
    17			printf("%d\t", array[i]);
    (gdb) 
    15		for (i = 0; i < len; ++i)
    (gdb) p i
    $5 = 7
    (gdb) i b
    Num     Type           Disp Enb Address            What
    1       breakpoint     keep y   0x0000000000400954 in main at main.c:17
        breakpoint already hit 3 times
    (gdb) d 1
    (gdb) display i
    1: i = 7
    (gdb) until
    19		printf("\n");
    1: i = 31
    (gdb) 
    35	67	89	87	65	54	24	58	92	100	24	46	78	99	200	55	44	33	22	11	71	2	4	86	8	9	
    22		selectionSort(array, len);
    1: i = 31
    (gdb) q
    A debugging session is active.

        Inferior 1 [process 6938] will be killed.

    Quit anyway? (y or n) y
    ```

#### 6.更多gdb命令
- [更多gdb命令请点击这里](https://www.cnblogs.com/xiaoshiwang/p/10755199.html)