### 技术交流QQ群:1027579432，欢迎你的加入！
### 本教程使用Linux发行版Centos7.0系统，请您注意~
#### 1.理解输入和输出
- 目前，已经知道了两种显示脚本输出的方法：
    - 在显示屏幕上显示输出；
    - 将输出重定向到文件中；
- 但是，实际中我们有时候也需要将一部分数据在显示器上显示，另一部分数据保存在文件中。下面将会介绍如何使用标准的Linux输入和输出系统来将脚本输出导向特定位置。
##### 1.1 标准文件描述符
- Linux系统将每个对象当作文件处理，这包括输入和输出进程。Linux用**文件描述符**来标识每个文件对象，文件描述符是一个非负整数，可以唯一标识会话中打开的文件，每个进程一次最多可以有9个文件描述符。出于特殊目的，bash shell保留了前3个文件描述符(0、1和2)，见下表所示：
    ```
    文件描述符                缩写                     描述
        0                    STDIN                   标准输入
        1                    STDOUT                  标准输出
        2                    STDERR                  标准错误
    ```
- **STDIN**：STDIN文件描述符代表shell的标准输入，对终端界面来说，标准输入是键盘。shell从STDIN文件描述符对应的键盘获得输入，在用户输入时处理每个字符。在使用输入重定向符<时，Linux会用重定向指定的文件来替换标准输入文件描述符。它会读取文件并提取数据，就像它是在键盘上输入的一样。
- 许多bash命令能接收STDIN的输入，尤其是没有在命令行上指定文件的话。例如，下面用cat命令处理STDIN输入的数据。
    ```
    [njust@njust tutorials]$ cat
    this is a cat.
    this is a cat.
    this is a second cat.
    this is a second cat.
    ```
- 当在命令行上只输入cat命令时，它会从STDIN接收输入。输入一行，cat命令就会显示出一行。也可以通过STDIN重定向符号强制cat命令接收来自另一个非STDIN文件的输入。你可以使用这种技术将数据输入到任何能从STDIN接收数据的shell命令中。如下例所示：
    ```
    [njust@njust tutorials]$ cat < test_file
    This is the first line.
    This is the second line.
    THis is the third line.
    ```
- **STDOUT**：STDOUT文件描述符代表shell的标准输出，在终端界面上，标准输出就是终端显示器。shell的所有输出(包括shell中运行的程序和脚本)会被定向到标准输出中，即显示器。默认情况下，大多数shell命令会将输出导向STDOUT文件描述符。如下例所示：
    ```
    [njust@njust tutorials]$ ls -l > test_file2

    # 结果
    [njust@njust tutorials]$ cat test_file2
    总用量 760
    -rwxrw-r--. 1 njust njust    178 4月   2 21:05 addem
    -rwxrw-r--. 1 njust njust    120 4月   2 21:28 bar10.sh
    -rwxrw-r--. 1 njust njust     98 4月   2 21:35 bar11.sh
    -rwxrw-r--. 1 njust njust     78 4月   2 21:48 bar12.sh
    -rwxrw-r--. 1 njust njust    214 4月   2 22:45 bar13.sh
    -rwxrw-r--. 1 njust njust    112 4月   2 22:47 bar14.sh
    -rwxrw-r--. 1 njust njust     99 4月   2 22:56 bar15.sh
    -rwxrw-r--. 1 njust njust    222 4月   3 20:55 bar16.sh
    ```
- **通过输出重定向符号，通常会显示到显示器的所有输出会被shell重定向到指定的重定向文件中**。也可以将数据追加到某个文件中，可以使用>>符号来完成。如下例所示：
    ```
    [njust@njust tutorials]$ who >> test_file

    # 结果
    [njust@njust tutorials]$ cat test_file
    This is the first line.
    This is the second line.
    THis is the third line.
    njust    :0           2020-04-08 20:54 (:0)
    njust    pts/0        2020-04-08 20:58 (:0)
    ```
- who命令生成的输出会被追加到test_file文件中已有的数据后面。但是，对脚本使用了标准输出重定向，会遇到一个问题：当命令生成错误消息时，shell并没有将错误消息重定向到输出重定向文件中。**shell创建了输出重定向文件，但错误消息却显示在显示器上**。如下例所示：
    ```
    [njust@njust tutorials]$ ls -al bad_file > test_file3
    ls: 无法访问bad_file: 没有那个文件或目录

    # 结果
    [njust@njust tutorials]$ cat test_file3
    ```
- **STDERR**：shell对错误消息的处理与普通输出分开的。shell通过特殊的STDERR文件描述符来处理错误消息，STDERR文件描述符代表shell的标准错误输出。shell或shell中运行的程序和脚本出错时，生成的错误消息都会发送到这个位置。默认情况下，STDERR文件描述符会和STDOUT文件描述符指向相同的地方(尽管分配给它们的文件描述符值不同)。即：默认情况下，错误消息也会输出到显示器上。但是，**STDERR并不会随着STDOUT的重定向而发生改变，尤其在希望将错误消息保存到日志文件中时，常常会想改变这种行为**。
##### 1.2 重定向错误
- 重定向STDERR数据与STDOUT数据没有太大差别，只要在使用重定向符号时，定义STDERR文件描述符就可以了。具体有如下几种方法实现：
- **只重定向错误**：如前面已知，STDERR文件描述符被设为2，可以选择只重定向错误消息，将该文件描述符放在重定向符号前。该值必须紧紧放在重定向符号前，否则不会起作用。如下所示：
    ```
    [njust@njust tutorials]$ ls -al bad_file 2> test_file3
    [njust@njust tutorials]$ cat test_file3
    ls: 无法访问bad_file: 没有那个文件或目录
    ```
- 如上例所示，错误消息不会出现在屏幕上了。该命令生成的任何错误消息都会保存在输出文件中。用上述方法，shell只会重定向错误消息，而非普通数据。下面是一个将STDOUT和STDERR消息混杂在同一个输出中的示例：
    ```
    [njust@njust tutorials]$ ls -al test bad_file test_file2 2> test_file4
    -rwxrw-r--. 1 njust njust  114 4月   4 21:37 test
    -rw-rw-r--. 1 njust njust 6394 4月   8 21:15 test_file2
    [njust@njust tutorials]$ cat test_file4
    ls: 无法访问bad_file: 没有那个文件或目录
    ```
- 上例中，ls命令的正常STDOUT输出仍然会发送到默认的STDOUT文件描述符，即显示器。由于该命令将文件描述符2的输出STDERR重定向到一个输出文件，shell会将生成的所有错误消息直接发送到指定的重定向文件中。
- **重定向错误和数据**：如果想重定向错误和正常输出，必须用两个重定向符号。需要在符号前面放上待重定向数据所对应的文件描述符，然后指向用于保存数据的输出文件。如下例所示：
    ```
    [njust@njust tutorials]$ ls -la test bad_file 2> stderr 1> stdout
    [njust@njust tutorials]$ cat stderr
    ls: 无法访问bad_file: 没有那个文件或目录
    [njust@njust tutorials]$ cat stdout
    -rwxrw-r--. 1 njust njust 114 4月   4 21:37 test
    ```
- 也可以将STDERR和STDOUT的输出重定向到同一个输出文件，因此bash shell提供了特殊的重定向符号\&>。如下所示：
    ```
    [njust@njust tutorials]$ ls -la test bad_file &> errAndout
    [njust@njust tutorials]$ cat errAndout 
    ls: 无法访问bad_file: 没有那个文件或目录
    -rwxrw-r--. 1 njust njust 114 4月   4 21:37 test
    ```
- 通过上例可以看出，当使用\&>符时，命令生成的所有输出都会发送到同一个位置，包括数据和错误。注意：其中一条错误消息出现的位置，**为了避免错误信息散落在输出文件中，相对于标准输出，bash shell自动赋予了错误消息更高的优先级，这样就能集中的浏览错误消息了**。

#### 2.在脚本中重定向输出
- 可以在脚本中使用STDOUT和STDERR文件描述符在多个位置生成输出，只要简单地重定向相应的文件描述符即可。有两种方法在脚本中重定向输出：
    - 临时重定向行输出
    - 永久重定向脚本中的所有命令
##### 2.1 临时重定向
- 如果有意在脚本中生成错误消息，可以将单独的一行输出重定向到STDERR。所要做的是使用输出重定向符来将输出信息重定向到STDERR文件描述符。在重定向到文件描述符时，你必须在文件描述符数字之前加一个&，如下所示：
    ```
    echo "This is an error message" >&2
    ```
- 上述这行脚本的STDERR文件描述符所指向的位置显示文本，而不是普通的STDOUT。就像下面实例那样：
    ```
    #!/bin/bash

    echo "This is an error." >&2   # 重点！！！
    echo "This is normal output."

    # 结果
    [njust@njust tutorials]$ ./foo1.sh 
    This is an error.
    This is normal output.

    # 默认情况下，Linux会将STDERR导向STDOUT。但是，如果你在运行脚本时重定向了STDERR，脚本中所有导向STDERR的文本都会被重定向。
    [njust@njust tutorials]$ ./foo1.sh 2> foo1-display 
    This is normal output.
    [njust@njust tutorials]$ cat foo1-display 
    This is an error.
    ```
- 在上例中，STDOUT显示的文本显示在了屏幕上，而发送给STDERR的echo语句的文本则被重定向到了输出文件。这个方法很适合在脚本中生成错误消息。
##### 2.2 永久重定向
- 如果脚本中有大量数据需要重定向，那重定向每个echo语句就很麻烦。因此，**你可以使用exec命令告诉shell在脚本执行期间重定向某个特定文件描述符**。如下例所示：
    ```
    #!/bin/bash

    exec 1> testout   # 重点！！！

    echo "This is a test of redirecting all output."
    echo "From a script to another file."
    echo "without having to redirect every individual line."

    # 结果
    [njust@njust tutorials]$ ./foo2.sh 
    [njust@njust tutorials]$ cat testout
    This is a test of redirecting all output.
    From a script to another file.
    without having to redirect every individual line.
    ```
- exec命令会启动一个新shell并将STDOUT文件描述符重定向到文件中。脚本中发给STDOUT的所有输出都会被重定向到文件中。可以在脚本执行过程中重定向STDOUT，如下例所示：
    ```
    #!/bin/bash


    exec 2> testerror

    echo "This is the start of the script."
    echo "now redirecting all output to another location."

    exec 1> testout

    echo "This output should go to the testout file."
    echo "but this should go to the testerror file." >&2

    # 结果
    [njust@njust tutorials]$ ./foo3.sh 
    This is the start of the script.
    now redirecting all output to another location.
    [njust@njust tutorials]$ cat testout 
    This output should go to the testout file.
    [njust@njust tutorials]$ cat testerror 
    but this should go to the testerror file.
    ```
- 当你只想将脚本的部分输出重定向到其他位置时，上述这个特性用起来很方便。但是，一旦重定向了STDOUT或STDERR，就很难再将它们重定向会原来的位置。
#### 3.在脚本中重定向输入
- 使用与STDOUT或STDERR相同的方法，将STDIN从键盘重定向到情况其他位置，exec命令允许你将STDIN重定向到Linux系统上的文件中。如：exec 0< testfile。这个命令会告诉shell它应该从文件testfile中获得输入，而不是STDIN。如下例所示：这是在脚本中从待处理的文件中读取数据的绝好方法，Linux系统管理员的一项日常任务就是从日志文件中读取数据并处理。
    ```
    #!/bin/bash

    exec 0< input-file


    count=1

    while read line
    do
    echo "Line #$count: $line"
    count=$[ $count + 1 ]
    done

    # 结果
    [njust@njust tutorials]$ ./foo4.sh 
    Line #1: This is the first line.
    Line #2: This is the second line.
    Line #3: This is the third line.
    ```
#### 4.创建自己的重定向
- 在脚本中重定向输入和输出时，并不局限0、1、2这三个默认的文件描述符。前面已经说过，在shell中最多可以有9个打开的文件描述符。**其他六个从3~8的文件描述符均可用于输入或输出重定向。可以将这些文件描述符中的任意一个分配给文件，然后在脚本中使用**。
##### 4.1 创建输出文件描述符
- 可以使用exec命令来给输出分配文件描述符，和标准的文件描述符一样，**一旦将另一个文件描述符分配给一个文件，这个重定向就会一直有效，直到你重新分配为止**。如下例所示：
    ```
    #!/bin/bash

    exec 3>test3out

    echo "This should display on the monitor."
    echo "and this should be stored in the file." >&3   # 注意这句！
    echo "Then this should be back on the monitor."

    # 结果
    [njust@njust tutorials]$ ./foo5.sh 
    This should display on the monitor.
    Then this should be back on the monitor.
    [njust@njust tutorials]$ cat test3out 
    and this should be stored in the file.
    ···
- 也可以不用创建新文件，而是使用exec命令来将输出追加到现有文件中，如下例所示：
    ```
    exec 3>> test3out
    ```
##### 4.2 重定向文件描述符
- 你可以分配另外一个文件描述符给标准文件描述符，反之亦然。这表示你可以将STDOUT的原来位置重定向到另一个文件描述符，然后再利用该文件描述符重定向回STDOUT。如下例所示：
    ```
    #!/bin/bash

    exec 3>&1  # 首先脚本将文件描述符3重定向到文件描述符1的当前位置，即STDOUT。因此，任何发送给文件描述符3的输出都将出现的屏幕上。
    exec 1>test4file  # 将STDOUT重定向到文件，shell现在会将发送给STDOUT的输出直接重定向到输出文件中。

    echo "This should store in the output file."
    echo "along with this line."

    exec 1>&3  
    echo "Now things should be back to normal."

    # 结果
    [njust@njust tutorials]$ ./foo6.sh 
    Now things should be back to normal.
    [njust@njust tutorials]$ cat test4file 
    This should store in the output file.
    along with this line.
    ```
##### 4.3 创建输入文件描述符
- 可以用和重定向输出文件描述符同样的方法重定向输入文件描述符，在重定向到文件之前，先将STDOUT文件描述符保存到另一个文件描述符，然后在读取完文件之后再将STDIN恢复到它原来的位置。
    ```
    #!/bin/bash

    exec 6<&0   # 文件描述符6用来保存STDIN的位置，然后脚本将STDIN重定向到一个文件

    exec 0<test4file


    count=1

    while read line   # read命令的所有输入都来自重定向后的STDIN(输入文件)
    do
    echo "Line #$count: $line"
    count=$[ $count + 1 ]
    done

    exec 0<&6   # 在读取了所有行之后，脚本会将STDIN重定向到文件描述符6，从而将STDIN恢复到原先的位置
    read -p "Are you done now? " answer
    case $answer in
    Y|y) echo "Goodbye!";;
    N|n) echo "Sorry,this is the end.";;
    esac

    # 结果
    [njust@njust tutorials]$ ./foo7.sh
    Line #1: This should store in the output file.
    Line #2: along with this line.
    Are you done now? y 
    Goodbye!
    ```
##### 4.4 创建读写文件描述符
- 可以打开单个文件描述符来作为输入和输出，可以用同一个文件描述符对同一文件进行读写。由于对同一文件进行数据读写，shell会维护一个内部指针，指明在文件中的当前位置。任何读或写都会从文件指针上次的位置开始，如果不小心它会产生意外的结果，如下例所示：
    ```
    #!/bin/bash


    exec 3<> test4file

    read line <&3

    echo "Read: $line"
    echo "This is a test line" >&3

    # 结果
    [njust@njust tutorials]$ ./foo8.sh 
    Read: This should store in the output file.
    [njust@njust tutorials]$ cat test4file
    This should store in the output file.
    This is a test line.
    ```
##### 4.5 关闭文件描述符
- 如果你创建了新的输入或输出文件描述符，shell会在脚本退出时自动关闭它们。然而在有些情况下，需要在脚本结束前手动关闭文件描述符。要关闭文件描述符，将它重定向到特殊符号\&-，例子如下：exec 3>&-。此语句会关闭文件描述符3，不再在脚本中使用它。如下例所示：
    ```
    #!/bin/bash


    exec 3> test8file

    echo "This is a test line of data." >&3

    exec 3>&-

    echo "This won't work." >&3

    # 结果
    [njust@njust tutorials]$ ./foo9.sh 
    ./foo9.sh:行10: 3: 错误的文件描述符
    ```
- 一旦关闭了文件描述符，就不能在脚本中向它写入任何数据，否则shell会生成错误消息。在关闭文件描述符时注意：**如果随后在脚本中打开了同一输出文件，shell会用一个新文件来替换已有文件。这意味着如果你输出数据，它就会覆盖已有文件**。如下例所示：
    ```
    [njust@njust tutorials]$ ./foo9.sh 
    This is a test line of data.
    [njust@njust tutorials]$ cat test8file
    This'll be bad.
    ```
#### 5.列出打开的文件描述符
- 虽然能用到的文件描述符只有9个，但是要记住哪个文件描述符被重定向到哪里很难。为了弄清楚，bash shell提供了lsof命令。lsof命令会列出整个Linux系统打开的所有文件描述符。在很多Linux系统中，lsof命令位于/usr/sbin目录下，以普通用户来运行时，必须通过全路径来引用。/usr/sbin/lsof它会显示当前Linux系统上打开的每个文件的有关信息。
- 有大量的命令行选项和参数可以用来帮助过滤lsof命令的输出。最常用的是-d和-p，-d允许指定要显示的文件描述符的编号，-p允许指定进程ID。要想知道进程的当前PID，可以用特殊环境变量\$\$。-a选项用来对其他两个选项的结果执行AND运算。lsof命令的默认输出中有7列信息，如下表所示：
    ```
    COMMAND                           正在运行的命令名的前9个字符
    PID                               进程的PID
    USER                              进程属主的登录名
    FD                                文件描述符号及访问类型
    TYPE                              文件的类型(CHR代表字符类型、BLK代表块型、DIR代表目录、REG代表常规文件)
    DEVICE                            设备的设备号(主设备和从设备)
    SIZE                              可选参数，表示文件的大小
    NODE                              本地文件的节点号
    NAME                              文件名
    ```
- 在打开多个替代性文本描述符的脚本中使用lsof命令，情况如下所示：
    ```
    #!/bin/bash

    exec 3> testfile_4
    exec 6> testfile_5
    exec 7< testfile

    /usr/sbin/lsof -a -p $$ -d0,1,2,3,6,7

    # 结果
    [njust@njust tutorials]$ ./foo10.sh 
    COMMAND   PID  USER   FD   TYPE DEVICE SIZE/OFF     NODE NAME
    foo10.sh 3821 njust    0u   CHR  136,0      0t0        3 /dev/pts/0
    foo10.sh 3821 njust    1u   CHR  136,0      0t0        3 /dev/pts/0
    foo10.sh 3821 njust    2u   CHR  136,0      0t0        3 /dev/pts/0
    foo10.sh 3821 njust    3w   REG  253,0        0 17004830 /home/njust/tutorials/testfile_4
    foo10.sh 3821 njust    6w   REG  253,0        0 17004842 /home/njust/tutorials/testfile_5
    foo10.sh 3821 njust    7r   REG  253,0       12 17005330 /home/njust/tutorials/testfile
    ```
#### 6.阻止命令输出
- 有时候不想显示脚本的输出，在将脚本作为后台进程运行时常见。如果运行在后台的脚本出现错误消息时，shell会通过电子邮件将它们发给进程的属主。但是，这十分麻烦。要解决这个问题，可以将STDERR重定向到一个叫作null文件的特殊文件。
- null文件里什么都没有，shell输出到null文件的任何数据都不会保存，全部都被丢失掉。Linux系统上null文件的标准位置是/dev/null，重定向到该位置的任何数据都会被丢失，不会显示。如下所示：
    ```
    [njust@njust tutorials]$ ls -al > /dev/null
    [njust@njust tutorials]$ cat /dev/null
    ```
- 避免出现错误消息，也无需保存它们的一个常用方法，如下所示：
    ```
    [njust@njust tutorials]$ ls -al curry-file testfile 2> /dev/null
    -rw-rw-r--. 1 njust njust 12 3月  17 14:42 testfile
    ```
- 也可以在输入重定向中将/dev/null作为输入文件，由于/dev/null文件中不含有任何内容，通常用它来快速清除现有文件中的数据，而不用先删除文件再重新创建。**这是清除日志文件的一个常用方法，因为日志文件必须时刻准备等待应用程序操作**。如下所示：
    ```
    [njust@njust tutorials]$ cat testfile
    Curry
    curry
    [njust@njust tutorials]$ cat /dev/null > testfile
    [njust@njust tutorials]$ cat testfile
    ```
#### 7.创建临时文件
- Linux使用/tmp目录来存放不需要永久保留的文件，大多数Linux发行版配置了系统在启动时自动删除/tmp目录的所有文件。系统上的任何用户都有权限在读写/tmp目录中的文件。mktemp命令可以在/tmp目录中创建一个唯一的临时文件。shell会创建这个文件，但不用默认的umask值。它会将文件的读写权限分配给文件的拥有者，并将你设置成文件的所有者。一旦创建了这个文件，你就在脚本中有了完整的读写权限，其他人没法访问它了(root用户除外)。
##### 7.1 创建本地临时文件
- 默认情况下，mktemp命令会在本地目录中创建一个文件，要用mktemp命令在本地目录中创建一个临时文件，只要指定一个文件名模板就行。模板可以包括任意文本文件名，在文件名末尾加上6个大写的X即可，mktemp命令会用6个字符替换6个X，从而保证文件名在目录中是唯一的。如下所示：
    ```
    [njust@njust tutorials]$ mktemp testing.XXXXXX
    testing.PFEqZP
    [njust@njust tutorials]$ ls -al testing*
    -rw-------. 1 njust njust 0 4月  14 22:06 testing.PFEqZP
    ```
##### 7.2 在/tmp目录中创建临时文件
- -t选项会强制mktemp命令在系统的临时目录来创建该文件，mktemp命令会返回用来创建临时文件的全路径，而不仅仅是文件名。如下所示：
    ```
    [njust@njust tutorials]$ mktemp -t test.XXXXXX
    /tmp/test.qCLPxp
    [njust@njust tutorials]$ ls -al /tmp/test*
    -rw-------. 1 njust njust 0 4月  14 22:12 /tmp/test.qCLPxp
    -rw-------. 1 njust njust 0 4月  14 22:12 /tmp/test.ZslvVI
    ```
##### 7.3 创建临时目录
- -d选项告诉mktemp命令来创建一个临时目录而不是一个临时文件，如下例所示：
    ```
    #!/bin/bash

    tempdir=$(mktemp -d dir.XXXXXX)
    cd $tempdir

    tempfile1=$(mktemp temp.XXXXXX)
    tempfile2=$(mktemp temp.XXXXXX)

    exec 7> $tempfile1
    exec 8> $tempfile2

    echo "Sending data to directory $tempdir."
    echo "This is a test line of data for $tempfile1" >&7
    echo "This is a test line of data for $tempfile2" >&8

    # 结果
    [njust@njust tutorials]$ ./foo11.sh
    Sending data to directory dir.TCOt7K.
    [njust@njust tutorials]$ cd dir.TCOt7K/
    [njust@njust dir.TCOt7K]$ ls -al
    总用量 16
    drwx------. 2 njust njust   44 4月  14 22:35 .
    drwxrwxr-x. 5 njust njust 4096 4月  14 22:35 ..
    -rw-------. 1 njust njust   44 4月  14 22:35 temp.5YibyX
    -rw-------. 1 njust njust   44 4月  14 22:35 temp.6tHZMW
    ```
#### 8.记录消息
- 将输出同时发送到显示器和日志文件，这种做法你不需要将输出重定向两次，只要用特殊的tee命令即可。tee命令相当于管道的一个T型转头。它将从STDIN过来的数据同时发往两处。一处是STDOUT，另一处是tee命令行所指定的文件名，tee命令格式：tee 文件名。
- 由于tee命令会重定向来自STDIN的数据，可以用它配合管道命令来重定向命令输出，如下所示：
    ```
    [njust@njust tutorials]$ date | tee teefile
    2020年 04月 14日 星期二 22:43:45 CST
    [njust@njust tutorials]$ cat teefile 
    2020年 04月 14日 星期二 22:43:45 CST
    ```
- 注意：**tee命令默认情况下，会在每次使用时覆盖输出文件的内容**。如下所示：
    ```
    [njust@njust tutorials]$ who | tee teefile 
    njust    :0           2020-04-14 21:17 (:0)
    njust    pts/0        2020-04-14 21:19 (:0)
    [njust@njust tutorials]$ cat teefile 
    njust    :0           2020-04-14 21:17 (:0)
    njust    pts/0        2020-04-14 21:19 (:0)
    ```
- 如果想将数据追加到文件中，必须用-a选项，如下所示：
    ```
    [njust@njust tutorials]$ date | tee -a teefile 
    2020年 04月 14日 星期二 22:46:14 CST
    [njust@njust tutorials]$ cat teefile 
    njust    :0           2020-04-14 21:17 (:0)
    njust    pts/0        2020-04-14 21:19 (:0)
    2020年 04月 14日 星期二 22:46:14 CST
    ```
#### 9.实例
- 文件重定向常用于脚本需要读入文件和输出文件时，下例中脚本做了两件事情。首先读取.csv文件的数据，再输出SQL INSERT语句来将数据插入到数据库中。如下例所示：
    ```
    #!/bin/bash

    outfile="members.sql"
    IFS=','


    while read lname fname address city state zip
    do
    cat >> $outfile << EOF
    INSERT INTO members (lname,fname,address,city,state,zip) VALUES
    ('$lname','$fname','$address','$city','$state','$zip');
    EOF
    done < ${1}

    # 结果
    [njust@njust tutorials]$ ./foo12.sh members.csv 
    [njust@njust tutorials]$ cat members.sql 
    INSERT INTO members (lname,fname,address,city,state,zip) VALUES
    ('Blum','Curry','123 Main St.','Chicago','IL','60601');
    INSERT INTO members (lname,fname,address,city,state,zip) VALUES
    ('Blum','Harden','123 Main St.','Chicago','IL','60601');
    INSERT INTO members (lname,fname,address,city,state,zip) VALUES
    ('Bresnahan','Barbara','456 Oak Ave.','Columbus','OH','43201');
    INSERT INTO members (lname,fname,address,city,state,zip) VALUES
    ('Bresnahan','Timothy','456 Oak Ave.','Columbus','OH','43201');
    ```
#### 10.资料下载
- [笔记，欢迎star,follow,fork......](https://github.com/cdlwhm1217096231/Linux/tree/master/Shell%E8%84%9A%E6%9C%AC%E7%BC%96%E7%A8%8B)