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
- 要想在脚本中的不同位置进行不同的捕获处理，只需要重新使用带有新选项的trap命令。如下例所示：
