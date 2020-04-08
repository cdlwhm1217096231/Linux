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