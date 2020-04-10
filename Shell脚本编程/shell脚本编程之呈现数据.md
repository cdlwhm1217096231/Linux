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
