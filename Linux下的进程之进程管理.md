### 一、进程的查看
- 不管在测试的时候、在实际的生产环境中，还是自己的使用过程中，难免会遇到一些进程异常的情况，所以 Linux 为我们提供了一些工具来查看进程的状态信息。我们可以通过 top 实时的查看进程的状态，以及系统的一些信息（如 CPU、内存信息等），我们还可以通过 ps 来静态查看当前的进程信息，同时我们还可以使用 pstree 来查看当前活跃进程的树形结构。
- 1.1 top工具的使用
    - top工具是我们常用的一个查看工具，能实时的查看我们系统的一些关键信息的变化。
    - 直接在终端下输入top后，会看到下图：
    ![top命令结果.png](https://upload-images.jianshu.io/upload_images/13407176-788b17fb17df6198.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
    - top 是一个在前台执行的程序，所以执行后便进入到上图的一个交互界面，正是因为交互界面我们才可以实时的获取到系统与进程的信息。在交互界面中我们可以通过一些指令来操作和筛选。在此之前我们先来了解显示了哪些信息。
    - top命令显示的第一行解释：
        - top	表示当前程序的名称
        - 11:05:18	表示当前的系统的时间
        - up 8 days,17:12	表示该机器已经启动了多长时间
        - 1 user	表示当前系统中只有一个用户
        - load average: 0.29,0.20,0.25	分别对应1、5、15分钟内cpu的平均负载。
        - load average参数解释：假设系统是单 CPU、单内核的，把系统比喻成是一条单向的桥，把CPU执行的任务比作汽车。
            - load = 0 的时候意味着这个桥上并没有车，cpu 没有任何任务；
            - load < 1 的时候意味着桥上的车并不多，一切都还是很流畅的，cpu 的任务并不多，资源还很充足；
            - load = 1 的时候就意味着桥已经被车给占满了，没有一点空隙，cpu 的已经在全力工作了，所有的资源都被用完了，当然还好，这还在能力范围之内，只是有点慢而已；
            - load > 1 的时候就意味着不仅仅是桥上已经被车占满了，就连桥外都被占满了，cpu 已经在全力工作，系统资源的用完了，但是还是有大量的进程在请求，在等待。若是这个值>２、>３，表示进程请求超过 CPU 工作能力的 2 到 ３ 倍。而若是这个值 > 5 说明系统已经在超负荷运作了。
    - 查看CPU的个数与核心数
        - 查看物理CPU的个数
            cat /proc/cpuinfo |grep "physical id"|sort |uniq|wc -l
        - 每个CPU的核心数
            cat /proc/cpuinfo |grep "physical id"|grep "0"|wc -l
    - 通过上面的指数我们可以得知load的临界值为 1 ，但是在实际生活中，比较有经验的运维或者系统管理员会将临界值定为0.7。这里的指数都是除以核心数以后的值，不要混淆了。
        - 若是 load < 0.7 并不会去关注它；
        - 若是 0.7< load < 1 的时候我们就需要稍微关注一下了，虽然还可以应付但是这个值已经离临界不远了；
        - 若是 load = 1 的时候我们就需要警惕了，因为这个时候已经没有更多的资源的了，已经在全力以赴了；
        - 若是 load > 5 的时候系统已经快不行了，这个时候你需要加班解决问题了
    - top命令显示的第二行解释：
        - Tasks: 26 total	进程总数
        - 1 running	1个正在运行的进程数
        - 25 sleeping	25个睡眠的进程数
        - 0 stopped	没有停止的进程数
        - 0 zombie	没有僵尸进程数
    - top命令显示的第三行解释(这一行基本上是对CPU的一个使用情况的统计)：
        - Cpu(s): 1.0%us	用户空间进程占用CPU百分比
        - 1.0% sy	内核空间运行占用CPU百分比
        - 0.0%ni	用户进程空间内改变过优先级的进程占用CPU百分比
        - 97.9%id	空闲CPU百分比
        - 0.0%wa	等待输入输出的CPU时间百分比
        - 0.1%hi	硬中断(Hardware IRQ)占用CPU的百分比
        - 0.0%si	软中断(Software IRQ)占用CPU的百分比
        - 0.0%st	(Steal time) 是 hypervisor 等虚拟服务中，虚拟 CPU 等待实际 CPU 的时间的百分比
    - CPU 利用率是对一个时间段内 CPU 使用状况的统计，通过这个指标可以看出在某一个时间段内 CPU 被占用的情况，而 Load Average 是 CPU 的 Load，它所包含的信息不是 CPU 的使用率状况，而是在一段时间内 CPU 正在处理以及等待 CPU 处理的进程数情况统计信息，这两个指标并不一样。
    - top命令显示的第四行解释:
        - 8176740 total	物理内存总量
        - 8032104 used	使用的物理内存总量
        - 144636 free	空闲内存总量(注:系统中可用的物理内存最大值并不是 free 这个单一的值，而是 free + buffers + swap 中的 cached 的和)
        - 313088 buffers	用作内核缓存的内存量
    - top命令显示的第五行解释:
        - total	交换区总量
        - used	使用的交换区总量
        - free	空闲交换区总量
        - cached	缓冲的交换区总量,内存中的内容被换出到交换区，而后又被换入到内存，但使用过的交换区尚未被覆盖
    - top命令显示的第六行解释(进程的使用情况):
        - PID	    进程id
        - USER	    该进程的所属用户
        - PR	    该进程执行的优先级 priority 值
        - NI	    该进程的 nice 值
        - VIRT	    该进程任务所使用的虚拟内存的总数
        - RES	    该进程所使用的物理内存数，也称之为驻留内存数
        - SHR	    该进程共享内存的大小
        - S	        该进程进程的状态: S=sleep R=running Z=zombie
        - %CPU	    该进程CPU的利用率
        - %MEM	    该进程内存的利用率
        - TIME+	    该进程活跃的总时间
        - COMMAND	该进程运行的名字
    - NICE值叫做静态优先级，是用户空间的一个优先级值，其取值范围是-20至19。这个值越小，表示进程”优先级”越高，而值越大“优先级”越低。-20优先级最高，0是默认的值，而19优先级最低。
    - PR值表示Priority值叫动态优先级，是进程在内核中实际的优先级值，进程优先级的取值范围是通过一个宏定义的，这个宏的名称是MAX_PRIO，它的值为140。Linux实际上实现了140个优先级范围，取值范围是从 0-139，这个值越小，优先级越高。而这其中的0~99是实时进程的值，而100 - 139是给用户的。
    -  top命令是一个前台程序，所以是一个可以交互的，常用交互命令如下：
![top常用交互命令.png](https://upload-images.jianshu.io/upload_images/13407176-e8e66f01cb0e63d4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 1.2 ps工具的使用
    - ps也是最常用的查看进程的工具之一，通过ps aux、ps axjf命令来了解一下它能带来哪些信息。
    - 参数介绍：
        ![ps命令参数介绍.png](https://upload-images.jianshu.io/upload_images/13407176-348fce4a88bff0cd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
        - TPGID栏写着-1的都是没有控制终端的进程，也就是守护进程
        - STAT表示进程的状态，而进程的状态有很多，如下表所示：
    ![STAT进程参数介绍.png](https://upload-images.jianshu.io/upload_images/13407176-ea3bfd8c7e5dafe8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
            - D是不能被中断睡眠的状态，处在这种状态的进程不接受外来的任何 signal，所以无法使用 kill 命令杀掉处于D状态的进程，无论是 kill，kill -9 还是 kill -15，一般处于这种状态可能是进程 I/O 的时候出问题了。
    - ps 工具有许多的参数，使用-l参数可以显示自己这次登陆的bash相关的进程信息罗列出来。
     ![ps -l命令.png](https://upload-images.jianshu.io/upload_images/13407176-9595bd8ff24f049b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
    - 如果查找其中的某个进程的话，还可以配合着 grep 和正则表达式一起使用。
    ![ps aux与正则表达式配合使用.png](https://upload-images.jianshu.io/upload_images/13407176-c31df0e61afdca70.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
    - 还可以在查看时，将连同部分的进程呈树状显示出来。
    ![ps axjf命令树状显示.png](https://upload-images.jianshu.io/upload_images/13407176-b5b50d0887a5e00f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
    -  自定义ps命令所需要显示的参数
    ![自定义ps命令的显示参数.png](https://upload-images.jianshu.io/upload_images/13407176-f801365ecd1b6280.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 1.3 pstree工具的使用
- 通过pstree可以很直接的看到相同的进程数量，最主要的还可以看到所有进程之间的相关性。
![pstree命令.png](https://upload-images.jianshu.io/upload_images/13407176-854a68899094a399.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
    - pstree工具参数介绍：
        - -A：各程序树之间以ASCII字元来连接
        - -p：同时列出每个process的PID
        - -u：同时列出每个process的所属账户名称
![pstree -up命令.png](https://upload-images.jianshu.io/upload_images/13407176-3e70f8d6c33f4759.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 1.4 kill命令的使用
- 当一个进程结束的时候或者要异常结束的时候，会向其父进程返回一个或者接收一个SIGHUP信号而做出的结束进程或者其他的操作，这个SIGHUP信号不仅可以由系统发送，我们可以使用 kill 来发送这个信号来操作进程的结束或者重启等等。
```
# 首先使用图形界面打开了 gedit、gvim，用 ps 可以查看到
ps aux

# 使用9这个信号强制结束 gedit 进程
kill -9 1608

# 我们再查找这个进程的时候就找不到了
ps aux | grep gedit 
```
![打开gedit.png](https://upload-images.jianshu.io/upload_images/13407176-b6d1c52c018fde01.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![强制结束gedit进程并进行验证.png](https://upload-images.jianshu.io/upload_images/13407176-110cabe8428cef8c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 1.5 进程的执行顺序
- 使用ps命令的时候可以看到大部分的进程都是处于休眠的状态，如果这些进程都被唤醒，那么该谁最先享受 CPU 的服务，后面的进程又该是一个什么样的顺序呢？进程调度的队列又该如何去排列呢？答案：就是靠该进程的优先级值来判定进程调度的优先级，而优先级的值就是上面所提到的PR与nice来控制与体现了。
- nice的值可以通过nice命令来修改的，而需要注意的是nice值可以调整的范围是-20 ~ 19，其中root有着至高无上的权力，既可以调整自己的进程也可以调整其他用户的程序，并且是所有的值都可以用，而普通用户只可以调制属于自己的进程，并且其使用的范围只能是 0 ~ 19，因为系统为了避免一般用户抢占系统资源而设置的一个限制。
```
# 打开一个程序放在后台，或者用图形界面打开
nice -n -5 vim &

# 用 ps 查看其优先级
ps -afxo user,ppid,pid,stat,pri,ni,time,command | grep vim

# 还可以用 renice 来修改已经存在的进程的优先级
renice -5 pid
```

    
