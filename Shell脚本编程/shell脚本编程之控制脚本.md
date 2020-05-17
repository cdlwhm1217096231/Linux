### 技术交流QQ群:1027579432，欢迎你的加入！
#### 1.引言
- 目前为止，运行脚本的唯一方式是以**实时模式**在命令行界面上直接运行。但是，**这并不是Linux上运行脚本的唯一方式**。
#### 2.处理信号量
- Linux利用信号与运行在系统中的进程进行通信。不同的Linux信号以及Linux如何用这些信号来停止、启动、终止进程。可以通过对脚本进行编程，使其在收到特定信号时执行某些命令，从而控制shell脚本的操作。
##### 2.1 重温Linux信号
- Linux系统和应用程序可以生成超过30个信号，下面列出了Linux编程时遇到的最常见Linux系统信号。
    ```
    信号                      值                          描述
    1                        SIGHUP                    挂起进程
    2                        SIGINT                    终止进程
    3                        SIGQUIT                   停止进程
    9                        SIGKILL                   无条件终止进程
    15                       SIGTERM                   尽可能终止进程
    17                       SIGSTOP                   无条件停止进程，但不是终止进程
    18                       SIGTSTP                   停止或暂停进程，但不终止进程
    19                       SIGCONT                   继续运行停止的进程
    ```
- **默认情况下**，bash shell会忽略收到的任何SIGQUIT(3)和SIGTERM(15)信号(正因为如此，交互式shell才不会意外停止)。但是，**bash shell会处理收到的SIGHUP(1)和SIGINT(2)信号**。
    - 如果bash shell收到了SIGHUP信号，比如你要离开一个交互式shell，它就会退出。但是在退出之前，它会将SIGHUP信号传递给所有由该shell所启动的进程。
    - 通过SIGINT信息，可以中断shell。Linux内核会停止为shell分配CPU处理时间。这种情况发生时，shell会将SIGINT信号传递给所有由它所启动的进程。
##### 2.2 生成信号
- bash shell允许使用键盘上的组合键生成两种基本的Linux信号，这个特性在需要**停止或暂停**失控程序时非常方便。
- **中断进程**：Ctrl+C组合键可以生成SIGINT信号，并将该信号发送给当前在shell中运行的所有进程，停止shell中当前运行的进程。如下例所示：
    ```
    $ sleep 100
    ^C
    ```
- **暂停进程**：可以在进程运行期间暂停进程，无需终止它。它可以在不终止进程的情况下，使你能够深入脚本内部仔细观察。Ctrl+Z组合键会生成一个SIGTSTP信号，暂停shell中运行的任何进程。暂停进程与中断进程不同：**暂停进程会让程序继续保留在内存中，并能从上次暂停的位置继续运行**。如下例所示：
    ```
    [njust@njust ~]$ sleep 10
    ^Z
    [1]+  已停止               sleep 10
    ```
- 上述示例中，方括号中的数字是shell分配的作业号，shell将shell中运行的每个进程称为**作业**，并为每个作业分配唯一的作业号。它会给第一个作业分配作业号1，第二个作业分配作业号2，依次类推。
- 如果你的shell会话中有一个已暂停的作业，在退出shell时，只要再输入一遍exit命令bash会提醒你。如下所示：
    ```
    [njust@njust ~]$ sleep 10
    ^Z
    [1]+  已停止               sleep 10
    [njust@njust ~]$ exit
    exit
    有停止的任务。
    ```
- 可以还有ps命令查看已经暂停的作业，如下所示：
    ```
    [njust@njust ~]$ sleep 10
    ^Z
    [2]+  已停止               sleep 10
    [njust@njust ~]$ ps -l
    F S   UID    PID   PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
    0 S  1000   3377   3369  0  80   0 - 29140 do_wai pts/0    00:00:00 bash
    0 T  1000   3642   3377  0  80   0 - 26989 do_sig pts/0    00:00:00 sleep
    0 R  1000   3733   3377  0  80   0 - 38312 -      pts/0    00:00:00 ps
    ```
- 在S列(进程状态)中，ps命令将已暂停作业的状态显示为T，可以通过已暂停作业的PID，就可以用kill命令来发送一个SIGKILL信号来终止它。如下例所示：
    ```
    [njust@njust ~]$ kill -9 3642
    [1]-  已杀死               sleep 10
    ```
##### 2.3 捕捉信号
- trap命令允许你来指定shell脚本要监控并从shell中拦截的Linux信号，如果脚本收到了trap命令中列出的信号，该信号不再由shell处理，而是交由本地处理。trap命令的格式如下：
    ```
    trap  commands signals
    ```
- 从上述trap命令的格式来看，在trap命令行上，只要列出想要shell执行的命令，以及一组用空格分开的待捕获的信号即可。如下例所示：使用trap命令来忽略SIGINT信号，并控制脚本的行为。
    ```
    #!/bin/bash

    trap "echo ' Sorry! I have trapped Ctrl-C'" SIGINT

    echo "This is a test script"

    count=1
    while [ $count -le 10 ]
    do
        echo "Loop #$count"
        sleep 2
        count=$[ $count +  1 ]
        done

    echo "This is the end of the test script"

    # 结果
    [njust@njust tutorials]$ ./control1.sh 
    This is a test script
    Loop #1
    Loop #2
    ^C Sorry! I have trapped Ctrl-C
    Loop #3
    Loop #4
    ^C Sorry! I have trapped Ctrl-C
    Loop #5
    Loop #6
    ^C Sorry! I have trapped Ctrl-C
    Loop #7
    Loop #8
    ^C Sorry! I have trapped Ctrl-C
    Loop #9
    Loop #10
    This is the end of the test script
    ```
- 上述示例程序使用trap命令在每次检测到SIGINT信号时，会显示一行简单的文本消息。捕获这些信号会阻止用户用bash shell组合键来Ctrl+C来停止程序运行。
##### 2.4 捕获脚本退出
- 除了在shell脚本中捕获信号，也可以在shell脚本退出的时候进行捕获。**要想捕获shell脚本的退出，只要在trap命令后加上EXIT信号即可**。如下例所示：
    ```
    #!/bin/bash

    trap "echo Goodbye..." EXIT

    count=1
    while [ $count -le 5 ]

    do
        echo "Loop #$count"
        sleep 1
        count=$[ $count + 1 ]
    done
    ```
- 上述示例程序中，当脚本运行到正常的退出位置时，捕获就被触发，shell会执行trap命令。如果提前退出脚本，同样能捕获到EXIT信号。
##### 2.5 修改或移除捕获
- 如果一个信号在捕获被修改前接收到的，那么脚本仍然会根据最初的trap命令进行处理。如下例所示：
    ```
    #!/bin/bash

    trap "echo ' Sorry... Ctrl-C is trapped.'" SIGINT

    count=1
    while [ $count -le 5 ]
    do 
        echo "Loop #$count"
        sleep 1
        count=$[ $count + 1 ]
    done

    trap "echo ' I modified the trap!'" SIGINT  # 此处捕捉被修改！

    count=1
    while [ $count -le 5 ]
    do
        echo "Second Loop #$count"
        sleep 1
        count=$[ $count + 1 ]
    done

    # 结果
    [njust@njust tutorials]$ ./control3.sh 
    Loop #1
    Loop #2
    Loop #3
    Loop #4
    ^C Sorry... Ctrl-C is trapped.
    Loop #5
    Second Loop #1
    Second Loop #2
    Second Loop #3
    ^C I modified the trap!
    ```
- 可以删除已经设置好的捕获：**只要在trap命令与希望恢复默认行为的信号列表之间加上两个破折号即可**，但如果信号是在捕获被移除前接收到的，那么脚本会按照原先trap命令中的设置进行处理，如下例所示：
    ```
    #!/bin/bash

    trap "echo ' Sorry... Ctrl-C is trapped.'" SIGINT

    count=1
    while [ $count -le 5 ]
    do
        echo "Loop #$count"
        sleep 1
        count=$[ $count + 1 ]
    done

    trap -- SIGINT   # 此处已经删除已经设置好的捕获，因此最前面设置的捕获SIGINT信号将会失效！
    echo "I just removed the trap"

    count=1
    while [ $count -le 5 ]
    do
        echo "Second Loop #$count"
        sleep 1
        count=$[ $count + 1 ]
    done

    # 结果
    [njust@njust tutorials]$ ./control4.sh 
    Loop #1
    Loop #2
    Loop #3
    ^C Sorry... Ctrl-C is trapped.
    Loop #4
    Loop #5
    I just removed the trap
    Second Loop #1
    Second Loop #2
    ^C
    ```
#### 3.以后台模式运行脚本
- 一些脚本可能要执行很长时间，而我们又不希望在命令行界面一直等待脚本执行结束。于是，可以采用后台运行的模式。**在后台模式中，进行运行时不会和终端会话上的STDIN、STDOUT、STDERR关联**。
##### 3.1 后台运行脚本
- **以后台模式运行脚本只需要在命令后加一个&即可**。如下例所示：
    ```
    #!/bin/bash

    count=1
    while [ $count -le 10 ]
    do
        sleep 1
        count=$[ $count + 1 ]
    done

    # 以后台模式运行脚本！！！
    [njust@njust tutorials]$ ./bg1.sh &
    [1] 4280
    ```
- 当&符号放到命令后时，它会将命令和bash shell分离开，将命令作为系统中的一个独立的后台进行运行，**结果中显示的第一行解释**：
    - \[1\]：shell分配给后台进程的作业号；
    - 4280：Linux系统分配给进程的进程ID（PID），**Linux系统上运行的每个进程都必须有一个唯一的PID**；
- **当后台进程结束时，它会在终端上显示出下面的一条信息**，该消息表示作业的作业号及作业状态Done，还有用于启动作用的命令。
    ```
    [1]+  完成                  ./bg1.sh
    ```
- 注意：**当后台进程运行时，它仍然会使用终端显示器来显示STDOUT和STDERR消息**，如下例所示：
    ```
    #!/bin/bash

    echo "Start the test script"
    count=1
    while [ $count -le 5 ]
    do
        echo "Loop #$count"
        sleep 2 
        count=$[ $count + 1 ]
    done

    echo "Test script is complete"

    # 结果
    [njust@njust tutorials]$ ./bg2.sh &
    [1] 4712
    Start the test script
    Loop #1
    Loop #2
    Loop #3
    Loop #4
    Loop #5
    Test script is complete
    [1]+  完成                  ./bg2.sh
    ```
##### 3.2 运行多个后台作业
- 可以在命令行中同时启动多个后台作业，每次启动新作业时，Linux系统都会为其分配一个新的作业号和PID。通过ps命令，可以看出所有脚本的状态。如下例所示：
    ```
    [njust@njust tutorials]$ ./bg2.sh &  ./bg3.sh & ./bg4.sh & ps
    [1] 5495
    [2] 5496
    [3] 5497
    Start the test script #4
    Loop #1
    Start the test script #2
    Loop #1
    Start the test script #3
    Loop #1
    PID TTY          TIME CMD
    5306 pts/0    00:00:00 bash
    5495 pts/0    00:00:00 bg2.sh
    5496 pts/0    00:00:00 bg3.sh
    5497 pts/0    00:00:00 bg4.sh
    5498 pts/0    00:00:00 ps
    5499 pts/0    00:00:00 sleep
    5500 pts/0    00:00:00 sleep
    5501 pts/0    00:00:00 sleep
    ```
- 在ps命令的输出中，每一个后台进程都和终端会话联系在一起。如果终端会话退出，那么后台进程也会随之退出。**如果希望运行在后台模式的脚本在登出控制台后能够继续运行，需要借助别的手段**。
#### 4.在非控制台下运行脚本
- **有时候，你会想在终端会话中启动shell脚本，然后让脚本一直以后台模式运行到结束，即使你已经退出了终端。可以使用nohup命令来实现**。nohup命令运行了另外一个命令来阻断所有发送给该进程的SIGHUP信号。这会在退出终端会话时，阻止进程退出。如下例所示：
    ```
    [njust@njust tutorials]$ nohup ./bg2.sh &
    [1] 5745
    nohup: 忽略输入并把输出追加到"nohup.out"
    ```
- 当使用nohup命令时，如果关闭该会话，脚本会忽略终端会话发送过来的SIGHUP信号。由于nohup命令会解除终端与进程的关联，进程也就不再同STDOUT和STDERR联系在一起。为了保存该命令产生的输出，nohup命令会自动将STDOUT和STDERR的消息重定向到一个名为nohup.out的文件中。nohup.out文件包含了通常会发送到终端显示器上的所有输出。如下所示：
    ```
    [njust@njust tutorials]$ cat nohup.out 
    Start the test script #2
    Loop #1
    Loop #2
    Loop #3
    Loop #4
    Loop #5
    Loop #6
    Loop #7
    Loop #8
    Loop #9
    Loop #10
    Test script is complete
    ```
#### 5.作业控制
- 作业控制：**启动、停止、终止及恢复作业的功能**。通过作业控制，就能完全控制shell环境中所有进程的运行方式了。
##### 5.1 查看作业
- 作业控制中的关键命令是jobs命令，**jobs命令允许查看shell当前正在处理的作业**。如下例所示：
    ```
    #!/bin/bash

    echo "Script process ID: $$"

    count=1
    while [ $count -le 10 ]
    do
        echo "Loop #$count"
        sleep 10
        count=$[ $count + 1 ]
    done

    echo "End of script ..."

    # 结果
    [njust@njust tutorials]$ ./work1.sh 
    Script process ID: 6157
    Loop #1
    Loop #2
    ^Z
    [1]+  已停止               ./work1.sh
    ```
- \$\$变量：**显示Linux系统分配给该脚本的PID**。可以从命令行启动该脚本，使用Ctrl-Z键来暂停脚本。利用\&将另一个作业作为后台进程启动，然后将脚本的结果重定向到文件中。如下所示：
    ```
    [njust@njust tutorials]$ ./work1.sh > work1.out &
    [2] 6264
    ```
- **jobs命令可以查看分配给shell的作业**，jobs命令会显示上面两个已停止/运行中的作业，以及它们的作业号和作业中使用的命令。如下所示：
    ```
    [njust@njust tutorials]$ jobs
    [1]+  已停止               ./work1.sh
    [2]-  运行中               ./work1.sh > work1.out &
    ```
- **要想查看作业的PID，可以在jobs命令中加入-l选项**。如下所示：
    ```
    [njust@njust tutorials]$ jobs -l
    [1]+  6157 停止                  ./work1.sh
    [2]-  6264 运行中               ./work1.sh > work1.out &
    ```
- **jobs命令参数如下所示**：
    ```
    参数                    描述
    -l                 列出进程的PID和作业号
    -n                 只列出上次shell发出的通知后改变的作业
    -p                 只列出作业的PID
    -r                 只列出运行中的作业
    -s                 只列出已停止的作业
    ```
- **jobs命令输出中的加号和减号，带加号的作业会被当做默认作业**。在使用作业控制命令时，如果未在命令行中指定任何作业号，该作业会被当成作业控制命令的操作对象。**当前的默认作业完成处理后，带减号的作业成为下一个默认作业。任何时候都只有一个带加号的作业和一个带减号的作业，不管shell中有多少个正在运行的作业**。下例表示了下一个作业在默认作业移除时是如何成为默认作业的。如下例所示：
    ```
    [njust@njust tutorials]$ ./work1.sh > work1.out &
    [1] 6721
    [njust@njust tutorials]$ ./work1.sh > work2.out &
    [2] 6729
    [njust@njust tutorials]$ ./work1.sh > work3.out &
    [3] 6738

    [njust@njust tutorials]$ jobs -l
    [1]   6721 运行中               ./work1.sh > work1.out &
    [2]-  6729 运行中               ./work1.sh > work2.out &
    [3]+  6738 运行中               ./work1.sh > work3.out &

    [njust@njust tutorials]$ kill 6738
    [3]+  已终止               ./work1.sh > work3.out
    [njust@njust tutorials]$ jobs -l
    [1]-  6721 运行中               ./work1.sh > work1.out &
    [2]+  6729 运行中               ./work1.sh > work2.out &

    [njust@njust tutorials]$ kill 6729
    [2]+  已终止               ./work1.sh > work2.out
    [njust@njust tutorials]$ jobs -l
    [1]+  6721 运行中               ./work1.sh > work1.out &

    [njust@njust tutorials]$ kill 6721
    [1]+  已终止               ./work1.sh > work1.out
    ```
##### 5.2 重启停止的作业
- **在bash作业控制中，可以将已停止的作业作为后台进程或前台进程重启。前台进程会接管你当前工作的终端，所以在使用该功能时要小心**。要以后台模式重启一个作业，可以使用bg命令加上作业号。如下例所示：
    ```
    [njust@njust tutorials]$ ./work1.sh 
    Script process ID: 6956
    Loop #1
    ^Z
    [1]+  已停止               ./work1.sh
    [njust@njust tutorials]$ bg
    [1]+ ./work1.sh &
    [njust@njust tutorials]$ jobs
    [1]+  运行中               ./work1.sh &
    ```
- 注意：**当作业被转入后台模式时，并不会列出其PID。如果有多个作业，需要在bg命令后加上作业号**。如下所示：
    ```
    [njust@njust tutorials]$ ./work1.sh 
    Script process ID: 7299
    Loop #1
    ^Z
    [1]+  已停止               ./work1.sh

    [njust@njust tutorials]$ ./work2.sh 
    Script process ID: 7307
    Loop #1
    ^Z
    [2]+  已停止               ./work2.sh

    [njust@njust tutorials]$ ./work3.sh 
    Script process ID: 7315
    Loop #1
    ^Z
    [3]+  已停止               ./work3.sh

    [njust@njust tutorials]$ bg 2   # bg 2用于将第二个作业置于后台模式！！！
    [2]- ./work2.sh &
    Loop #2
    
    [njust@njust tutorials]$ jobs
    [1]-  已停止               ./work1.sh
    [2]   运行中               ./work2.sh &
    [3]+  已停止               ./work3.sh
    ```
- 注意：**当使用jobs命令时，它列出了作业及其状态，即使是默认作业当前并没有处于后台模式**。要以前台模式重启已暂停作业，可使用带有作用号的fg命令。由于作业是以前台模式运行的，直到该作业完成后，命令行界面才会出现提示符。如下所示：
    ```
    [njust@njust tutorials]$ ./work1.sh 
    Script process ID: 7451
    Loop #1
    ^Z  
    [2]+  已停止               ./work1.sh

    [njust@njust tutorials]$ ./work2.sh 
    Script process ID: 7459
    Loop #1
    ^Z
    [3]+  已停止               ./work2.sh

    [njust@njust tutorials]$ ./work3.sh 
    Script process ID: 7467
    Loop #1
    ^Z
    [4]+  已停止               ./work3.sh

    [njust@njust tutorials]$ fg 2
    ./work1.sh
    Loop #2
    Loop #3
    Loop #4
    Loop #5
    Loop #6
    Loop #7
    Loop #8
    Loop #9
    Loop #10
    End of script ...
    [njust@njust tutorials]$ 
    ```
#### 6.调整谦让度