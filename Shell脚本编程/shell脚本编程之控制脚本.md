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
- 在多任务操作系统中，内核负责将CPU时间分配给系统上运行的每个进程。**调度优先级**是内核分配给进程的CPU时间。在Linux系统中，由shell启动的所有进程的调度优先级默认都是相同的。
- 调度优先级是一个整数值，从-20(最高优先级)~+19(最低优先级)。**默认情况下，bash shell以优先级0来启动所有进程**。越低的值即优先级越高，获得CPU时间的机会就越高。
##### 6.1 nice命令
- nice命令允许你设置命令启动时的调度优先级，要让命令以更低的优先级允许，只要用nice -n命令来指定新的优先级级别。如下例所示：
    ```
    [njust@njust tutorials]$ nice -n 10 ./work1.sh > work1.out &
    [1] 3484
    [njust@njust tutorials]$ ps -p 3484 -o pid,ppid,ni,cmd
    PID   PPID  NI CMD
    3484   2985  10 /bin/bash ./work1.sh
    ```
- 注意：**必须将nice命令和要启动的命令放在同一行中**。上例中，ps命令的输出验证了谦让度值(NI列)已经被调整为10。
- 但是，如果想要提高某个命令的优先级，**nice命令会阻止普通用户来提高命令的优先级**。因为nice命令的-n选项并不是必须的，只需要在破折号后面跟上优先级即可。如下例所示：
    ```
    # 提升优先级失败的情况
    [njust@njust tutorials]$ nice -n -10 ./work1.sh > work1.out &
    [1] 3570
    [njust@njust tutorials]$ nice: 无法设置优先级: 权限不够
    ^C
    # 提升优先级成功的情况
    [njust@njust tutorials]$ nice -10 ./work1.sh > work1.out &
    [1] 3760
    [njust@njust tutorials]$ ps -p 3760 -o pid,ppid,ni,cmd
    PID   PPID  NI CMD
    3760   2985  10 /bin/bash ./work1.sh
    ```
##### 6.2 renice命令
- 有时候需要改变系统上已运行命令的优先级，通过renice命令即可实现。**它允许你指定运行进程的PID来改变它的优先级**。如下例所示：
    ```
    [root@njust tutorials]# ./work1.sh &
    [1] 4612
    Script process ID: 4612
    [root@njust tutorials]# ps -p 4612 -o pid,ppid,ni,cmd
    PID   PPID  NI CMD
    4612   4263   0 /bin/bash ./work1.sh
    [root@njust tutorials]# renice -n 10 -p 4612
    4612 (进程 ID) 旧优先级为 0，新优先级为 10
    [root@njust tutorials]# ps -p 4612 -o pid,ppid,ni,cmd
    PID   PPID  NI CMD
    4612   4263  10 /bin/bash ./work1.sh
    ```
- renice命令会自动更新**当前运行**进程的调度优先级，必须以root账户登录后才能使用！renice也有一些限制：
    - 只能对属于你的进程执行renice；
    - 只能通过renice降低进程的优先级；
    - root用户可以通过renice来任意调整进程的优先级；
#### 7.定时任务
- 有时候，我们需要在某个预先设定的时间开始运行脚本，Linux系统提供了多个在预选时间运行脚本的方法：**at命令和cron表**。
##### 7.1 用at命令来计划执行作业
- at命令允许指定Linux系统何时运行脚本，at命令会将作业提交到队列中，指定shell何时运行该作业。at的守护进程atd会以后台模式运行，检查作业队列来运行作业。atd守护进程会检查系统上的一个特殊目录/var/spool/at来获取用at命令提交的作业。**默认情况下，atd守护进程会每60s检查一下该目录**。
- **1.at命令的格式**
    ```
    at [-f filename] time
    ```
- 默认情况下，at命令会将STDIN的输入放到队列中。**可以使用-f选项来指定用于读取命令(脚本文件)的文件名。time参数指定了Linux系统何时运行该作业。如果你指定的时间已经错过，at命令会在第二天的那个时刻运行指定的作业**。
- 如何指定时间？at命令能够识别多种不同的时间格式。如下所示：
    - 标准的小时和分钟格式，如21:00；
    - AM/PM指示符，如21:00 PM；
    - 特定可命令时间，如now noon midnight等；
    - 标准日期格式，如MMDDYY MM/DD/YY DD.MM.YY
    - 文本日期，如Jul 4或Dec 25；
    - 指定时间增量：
        - 当前时间+25 min；
        - 明天10:15 PM；
        - 10：15+7天；
- 在使用at命令时，该作业会被提交到作业队列中。作业队列会保存通过at命令提交的待处理的作业。针对不同的优先级，存在26种不同的作业队列，作业队列通过用小写字母a~z和大写字母A~Z来表示。**作业队列的字母排序越高，作业运行的优先级就越低(更高的nice值)**。默认情况下，at的作业会被提交到a作业队列。如果想以更高的优先级运行作业，可以用-q参数指定不同的队列字母。
- **2.获取作业的输出**
- 当作业在Linux系统上运行时，显示器并不会关联到该作业。取而代之的是，Linux系统会将提交该作业的用户的电子邮箱地址作为STDOUT和STDERR。任何发到STDOUT或STDERR的输出都会通过邮件发送给该用户。如下例所示：
    ```
    #!/bin/bash

    echo "This script run at $(date +%B%d,%T)"
    sleep 5
    echo "This is the script's end"

    # 结果
    [njust@njust tutorials]$ at -f at.sh now
    job 1 at Mon May 18 21:15:00 2020
    您在 /var/spool/mail/njust 中有新邮件
    ```
- 使用e-mail作为at命令的输出很不方便，因此在使用at命令时，最好在脚本中对STDOUT和STDERR进行重定向。**如果不想在at命令中使用邮件或重定向，最好加上-M选项来屏蔽作业产生的输出信息**。如下所示：
    ```
    #!/bin/bash

    echo "This script run at $(date +%B%d,%T)" > at1.out
    echo >> at1.out
    sleep 3
    echo "This is the script's end" >> at1.out

    # 结果
    [njust@njust tutorials]$ at -M -f at1.sh now
    job 4 at Mon May 18 21:20:00 2020
    [njust@njust tutorials]$ cat at1.out 
    This script run at 五月18,21:20:29

    This is the script's end
    [njust@njust tutorials]$ cat at1.sh 
    ```
- **3.列出等待的作业**
- atq命令可以查看系统中有哪些作业在等待。如下所示：
    ```
    [njust@njust tutorials]$ at -M -f at1.sh teatime
    job 5 at Tue May 19 16:00:00 2020
    [njust@njust tutorials]$ at -M -f at1.sh tomorrow
    job 6 at Tue May 19 21:23:00 2020
    [njust@njust tutorials]$ at -M -f at1.sh 23:00
    job 7 at Mon May 18 23:00:00 2020
    [njust@njust tutorials]$ at -M -f at1.sh now
    job 8 at Mon May 18 21:24:00 2020

    [njust@njust tutorials]$ atq
    5	Tue May 19 16:00:00 2020 a njust
    6	Tue May 19 21:23:00 2020 a njust
    7	Mon May 18 23:00:00 2020 a njust
    ```
- **4.删除作业**
- 一旦知道哪些作业在作业队列中等待，就能使用atrm命令来删除等待中的命令。如下所示：
    ```
    [njust@njust tutorials]$ atq
    5	Tue May 19 16:00:00 2020 a njust
    6	Tue May 19 21:23:00 2020 a njust
    7	Mon May 18 23:00:00 2020 a njust
    [njust@njust tutorials]$ atrm 5
    [njust@njust tutorials]$ atq
    6	Tue May 19 21:23:00 2020 a njust
    7	Mon May 18 23:00:00 2020 a njust
    ```
##### 7.2 安排需要定期执行的脚本
- 使用at命令预设时间安排脚本执行很方便，但是如果想每天的同一时间都执行该脚本，就不再使用at命令不断提交作业了。**Linux系统使用cron程序来安排要定期执行的作业**。cron程序会在后台运行并检查一个特殊的表(cron时间表)，以获得已安排执行的作业。
- **1.cron时间表**：cron时间表采用一种特别的格式来指定作业何时运行，格式如下所示：
    ```
    min hour day month week 具体的命令
    ```
- **cron时间表允许你使用特定值、取值范围(如1~5)或者通配符(\*)来指定条目**。如下所示：
    ```
    15 10 * * * command  # 每天的10：15执行一次命令
    ```
- 可以用三字符的文本值(mon、tue、wed、thu、fri、sat、sun)或数值(0为周日，6为周六)来指定week的值。如何设置一个在每个月的最后一天执行的命令？常用的方法是**加一条使用date命令的if-then语句来检查明天的日期是否为01**。如下所示：
    ```
    00 12 * * * if [ `date +%d -d tomorrow` = 01 ]; then ; command
    ```
- 具体的命令中**必须指定要运行的命令或脚本的全路径名**，如下例所示：
    ```
    15 10 * * * /home/njust/tutotials/test.sh > test.out
    ```
- **2.构建cron时间表**
- 每个系统用户都可以用自己的cron时间表来运行安排好的任务，**Linux提供了crontab命令来处理cron时间表**。要列出已有的cron时间表，可以用-l选项。如下所示：
    ```
    [njust@njust tutorials]$ crontab -l
    no crontab for njust
    ```
- **默认情况下，用户cron时间表文件并不存在**。要为cron时间表添加条目，可以用-e选项。在添加条目时，crontab命令会启动一个文本编辑器，使用已有的cron时间表作为文件内容。
- **3.浏览cron目录**
- 如果你创建的脚本对精确的执行时间要求不高，用预配置的cron脚本目录会更方便。有4个基本目录：hourly、daily、monthly、weekly。如果脚本需要每天执行一次，只要将脚本复制到daily目录，cron就会每天执行它。如下所示：
    ```
    [njust@njust ~]$ ls /etc/cron.*ly
    /etc/cron.daily:
    logrotate  man-db.cron  mlocate

    /etc/cron.hourly:
    0anacron  mcelog.cron

    /etc/cron.monthly:

    /etc/cron.weekly:
    ```
**4.anacron程序**
- cron程序的唯一问题是：它假设Linux是7\*24小时运行的，除非将Linux系统当成服务器环境来运行，否则假设不会成立。如果某个作业在cron时间表中安排运行的时间已到，但这时Linux系统处于关机状态，那么这个作业就不会执行。当系统开机时，cron程序不会再去运行那些错过的作业。**要解决这个问题，许多Linux发行版包含了anacron程序**。
- 如果anacron知道某个作业错过了执行时间，它会尽快运行该作业。**这个功能常用于日志维护的脚本**。anacron程序只会处理位于cron目录的程序，如/etc/cron.monthly。它使用时间戳来决定作业是否在正确的计划间隔内运行。每个cron目录都有一个时间戳文件，该文件位于/var/spool/anacron。如下所示：
    ```
    [root@njust njust]# cat /var/spool/anacron/cron.monthly
    20200517
    ```
- anacron程序使用自己的时间表(通常位于/etc/anacrontab)来检查作业目录。如下所示：
    ```
    [root@njust njust]# cat /etc/anacrontab
    # /etc/anacrontab: configuration file for anacron

    # See anacron(8) and anacrontab(5) for details.

    SHELL=/bin/sh
    PATH=/sbin:/bin:/usr/sbin:/usr/bin
    MAILTO=root
    # the maximal random delay added to the base delay of the jobs
    RANDOM_DELAY=45
    # the jobs will be started during the following hours only
    START_HOURS_RANGE=3-22

    #period in days   delay in minutes   job-identifier   command
    1	5	cron.daily		nice run-parts /etc/cron.daily
    7	25	cron.weekly		nice run-parts /etc/cron.weekly
    @monthly 45	cron.monthly		nice run-parts /etc/cron.monthly
    ```
- anacron时间表的基本格式如下所示：
    - period：定义了作业多久运行一次，以天为单位。anacron程序用此来检查作业的时间戳文件；
    - delay：指定系统启动后anacron程序需要等待多少分钟再开始运行错过的脚本；
    - command：包含了run-parts程序(**负责运行目录中传给它的任何脚本**)和一个cron脚本目录名；
    - identifier：一种特别的非空字符串，用于唯一标识日志消息和错误邮件中的作业；
    ```
    period delay identifier command
    ```
- 注意：**anacron程序不会运行位于 /etc/cron.hourly的脚本。因为anacron程序不会处理执行时间需求小于一天的脚本**。
##### 7.3 使用新shell启动脚本
- 如果每次运行脚本的时候都能够启动一个新的bash shell，将会非常方便。当用户登录bash shell时需要运行相关的启动文件，基本上不同发行版的Linux系统按照下列顺序所找到的第一个文件会被运行，其余的文件将会被忽略：
    - \$HOME/.bash_profile
    - \$HOME/.bash_login
    - \$HOME/.profile
- **因此，需要将在登录时运行的脚本放在上面的第一个文件中**。每次启动一个新的shell时，bash shell都会运行.bashrc文件。可以如下例所示进行验证：
    ```
    # .bashrc

    # Source global definitions
    if [ -f /etc/bashrc ]; then
        . /etc/bashrc
    fi

    # Uncomment the following line if you don't like systemctl's auto-paging feature:
    # export SYSTEMD_PAGER=

    # User specific aliases and functions

    echo "I'm in a new shell!"

    # 结果
    [njust@njust ~]$ bash
    I'm in a new shell!
    ```
- .bashrc文件通常也是通过某个bash启动文件来运行的。**因为.bashrc文件会运行两次**：一次是当你登入bash shell时，另一次是当你启动一个bash shell时。如果你需要一个脚本在两个时刻都得以运行，可以把这个脚本放进该文件中。
#### 8.资料下载
- [笔记，欢迎star,follow,fork......](https://github.com/cdlwhm1217096231/Linux/tree/master/Shell%E8%84%9A%E6%9C%AC%E7%BC%96%E7%A8%8B)
