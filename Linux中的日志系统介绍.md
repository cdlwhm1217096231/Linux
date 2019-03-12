### 一、常见的日志
- 日志是一个系统管理员，一个运维人员，甚至是开发人员不可或缺的东西，系统用久了偶尔也会出现一些错误，我们需要日志来给系统排错，在一些网络应用服务不能正常工作的时候，我们需要用日志来做问题定位，日志还是过往时间的记录本，我们可以通过它知道我们是否被不明用户登录过等等。
- 在 Linux 中大部分的发行版都内置使用 syslog 系统日志，那么通过前期的课程了解到常见的日志一般存放在 /var/log 中，我们来看看其中有哪些日志:
![查看日志.png](https://upload-images.jianshu.io/upload_images/13407176-1ee34ef73ee8d66f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 1.根据图中所显示的日志，我们可以根据服务对象粗略的将日志分为两类:
    - 系统日志
    - 应用日志
- 系统日志：主要是存放系统内置程序或系统内核之类的日志信息如 alternatives.log 、btmp 等等
- 应用日志：主要是我们装的第三方应用所产生的日志如 tomcat7 、apache2 等等。
- 2.查看常见的日志有哪些，都记录了如下的信息：
![日志包含的信息.png](https://upload-images.jianshu.io/upload_images/13407176-0588f1f32529e8c2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- alternatives.log中的日志信息如下，可以得到程序作用，日期，命令，成功与否的返回码。
![alternatives文件.png](https://upload-images.jianshu.io/upload_images/13407176-38c03966fcfa56e5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 使用less auto.log来查看auto.log中的信息，可以得到有日期、ip地址的来源及用户工具。
![auto文件.png](https://upload-images.jianshu.io/upload_images/13407176-37b5fd91f948ad2e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- apt文件夹中的日志信息，其中有两个日志文件history.log和term.log，两个日志文件的区别在于history.log主要记录了进行了哪个操作，相关的依赖有哪些；而term.log则是较为具体的操作，主要是下载包，打开包，安装包等细节操作。
    ```
    less /var/log/apt/history.log
    less /var/log/apt/term.log
    ```
    ![开始时的history.log文件.png](https://upload-images.jianshu.io/upload_images/13407176-bf7c1f0413fb94a5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
    ![开始时的term.log文件.png](https://upload-images.jianshu.io/upload_images/13407176-b0bdb40bce1b4969.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 进行安装git程序(sudo apt-get install git)后，由于之前已经安装好了git，所以这里执行的操作就是一个更新的操作。
![安装git.png](https://upload-images.jianshu.io/upload_images/13407176-4aec217db962eba7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![git安装后的history.log文件.png](https://upload-images.jianshu.io/upload_images/13407176-cd1de7da3ec699ba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![git安装后的term.log文件.png](https://upload-images.jianshu.io/upload_images/13407176-285d9b601522153f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 其他的日志格式都类似于之前查看的日志，主要是时间，操作。这其中有两个比较特殊的日志，其查看的方式比较与众不同，因为这两个日志并不是ASCII文件而是被编码成了二进制文件，所以不能直接使用less cat more等这样的工具进行查看，这两个日志文件是wtmp,lastlog。
![lastlog日志文件.png](https://upload-images.jianshu.io/upload_images/13407176-19887154858d1454.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 查看的方法是使用last与lastlog工具来提取其中的信息：
![正确查看lastlog和wtmp文件的方法.png](https://upload-images.jianshu.io/upload_images/13407176-d2a02d6049bb11e1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 二、配置的日志
- 1.日志是如何产生的？主要通过两种方式：
    - 一种是由软件开发商自己来自定义日志格式然后指定输出日志位置；
    - 一种方式就是 Linux 提供的日志服务程序，而我们这里系统日志是通过 syslog 来实现，提供日志管理服务。
- 2.syslog 是一个系统日志记录程序，在早期的大部分 Linux 发行版都是内置 syslog，让其作为系统的默认日志收集工具，虽然随着时代的进步与发展，syslog 已经年老体衰跟不上时代的需求，所以它被 rsyslog 所代替了，较新的 Ubuntu、Fedora 等等都是默认使用 rsyslog 作为系统的日志收集工具。
- 3.rsyslog的全称是 rocket-fast system for log，它提供了高性能，高安全功能和模块化设计。rsyslog 能够接受各种各样的来源，将其输入，输出的结果到不同的目的地。rsyslog 可以提供超过每秒一百万条消息给目标文件，能实时收集日志信息的程序是有其守护进程的，如 rsyslog 的守护进程便是 rsyslogd
- 下面是开启rsyslog这项服务：
    ```
    sudo apt-get update
    sudo apt-get install -y rsyslog
    sudo service rsyslog start
    ps aux | grep syslog
    ```
    ![开启rsyslog服务.png](https://upload-images.jianshu.io/upload_images/13407176-f078157145ab70d8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 由于rsyslog是一个服务，那么它便是可以配置，系统为提供一些自定义的服务。首先看 rsyslog 的配置文件是什么样子的，rsyslog 的配置文件有两个：
    - 一个是 /etc/rsyslog.conf，主要是配置的环境，也就是 rsyslog 加载什么模块，文件的所属者等
    - 一个是 /etc/rsyslog.d/50-default.conf，主要是配置的 Filter Conditions
    ```
    vim /etc/rsyslog.conf 
    vim /etc/rsyslog.d/50-default.conf
    ```
    ![rsyslog.conf文件.png](https://upload-images.jianshu.io/upload_images/13407176-0b504c928eff4c55.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
    ![rsyslog.d文件.png](https://upload-images.jianshu.io/upload_images/13407176-4aba944aa5cd4770.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 查看rsyslog的结构框架，数据流的走向：
![rsyslog的结构框架和数据流走向.png](https://upload-images.jianshu.io/upload_images/13407176-dafb24143e018438.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 通过上面这个简单的流程图我们可以知道 rsyslog 主要是由 Input、Output、Parser 这样三个模块构成的，并且了解到数据的简单走向，首先通过 Input module 来收集消息，然后将得到的消息传给 Parser module，通过分析模块的层层处理，将真正需要的消息传给 Output module，然后便输出至日志文件中。
- 上文提到过 rsyslog 号称可以提供超过每秒一百万条消息给目标文件，怎么只是这样简单的结构。我们可以通过下图来做更深入的了解：
![rsyslog文件深度剖析.png](https://upload-images.jianshu.io/upload_images/13407176-9cfac339a1daf293.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- rsyslog 架构如上图所示，从图中我们可以很清楚的看见，rsyslog 还有一个核心的功能模块便是Queue，也正是因为它才能做到如此高的并发。第一个模块便是Input，该模块的主要功能就是从各种各样的来源收集 messages，通过这些接口实现：
![Input的接口实现.png](https://upload-images.jianshu.io/upload_images/13407176-c20b0733c40b2919.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- Output中也有许多可用的接口，可以通过man或者官方的文档查看，这些模块接口的使用需要通过 $ModLoad 指令来加载，那么返回上文的图rsyslog.conf中，配置生效的头两行可以看懂了，默认加载了 imklog、imuxsock 这两个模块。在配置中 rsyslog 支持三种配置语法格式：
    - sysklogd
    - legacy rsyslog
    - RainerScript
- sysklogd 是老的简单格式，一些新的语法特性不支持。而 legacy rsyslog 是以 dollar 符($)开头的语法，在 v6 及以上的版本还在支持，就如上文所说的 $ModLoad 还有一些插件和特性只在此语法下支持。而以 $ 开头的指令是全局指令，全局指令是 rsyslogd 守护进程的配置指令，每行只能有一个指令。 RainnerScript 是最新的语法。在官网上rsyslog 大多推荐这个语法格式来配置。老的语法格式（sysklogd & legacy rsyslog）是以行为单位。新的语法格式（RainnerScript）可以分割多行。注释有两种语法:
    - 井号 #
    - C风格 /* .. */
- 执行顺序: 指令在 rsyslog.conf 文件中是从上到下的顺序执行的。
- 模板是rsyslog一个重要的属性，它可以控制日志的格式，支持类似 template() 语句的基于 string 或 plugin 的模板，通过它我们可以自定义日志格式。legacy 格式使用 $template 的语法，不过这个在以后要移除，所以最好使用新格式 template()，以免未来突然不工作了也不知道为什么。
- 模板定义的形式有四种，适用于不同的输出模块，一般简单的格式，可以使用 string 的形式，复杂的格式，建议使用 list 的形式，使用 list 的形式，可以使用一些额外的属性字段（property statement），如果不指定输出模板，rsyslog 会默认使用 RSYSLOG_DEFAULT。
- 了解完rsyslog 环境的配置文件之后，我们看/etc/rsyslog.d/50-default.conf这个配置文件，这个文件中主要是配置的Filter Conditions，也就是我们在流程图中所看见的 Parser & Filter Engine,它的名字叫 Selectors 是过滤 syslog 的传统方法，他主要由两部分组成，facility 与 priority，其配置格式如下：
    ```
    facility.priority　　　　　log_location
    ```
- 其中一个 priority 可以指定多个 facility，多个 facility 之间使用逗号割开。rsyslog 通过 facility 的概念来定义日志消息的来源，以便对日志进行分类，facility 的种类有：
![facility种类.png](https://upload-images.jianshu.io/upload_images/13407176-f193897603649f49.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 而另外一部分 priority 也称之为 serverity level，除了日志的来源以外，对统一源产生日志消息还需要进行优先级的划分，而优先级的类别有以下几种：
![优先级类别.png](https://upload-images.jianshu.io/upload_images/13407176-3b60d92538e69f3d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 查看系统中的配置文件/etc/rsyslog.d/50-default.conf:
![查看rsyslog.d文件.png](https://upload-images.jianshu.io/upload_images/13407176-0d0e908b086e963a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
auth,authpriv.*       /var/log/auth.log
```
- 解释:auth 与 authpriv 的所有优先级的信息全都输出于 /var/log/auth.log 日志中;
```
kern.*      -/var/log/kern.log
```
- 解释：而其中有类似于这样的配置信息意思有细微的差别,- 代表异步写入，也就是日志写入时不需要等待系统缓存的同步，也就是日志还在内存中缓存也可以继续写入无需等待完全写入硬盘后再写入。通常用于写入数据比较大时使用。
- 4.与日志相关的还有一个还有常用的命令 logger,logger 是一个 shell 命令接口，可以通过该接口使用 Syslog 的系统日志模块，还可以从命令行直接向系统日志文件写入信息。
```
#首先将syslog启动起来
sudo service rsyslog start
#向 syslog 写入数据
ping 127.0.0.1 | logger -it logger_test -p local3.notice &
#查看是否有数据写入
sudo tail -f /var/log/syslog
```
![logger.png](https://upload-images.jianshu.io/upload_images/13407176-e27c1be5f531db68.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 从上图中我们可以看到我们成功的将 ping 的信息写入了 syslog 中，格式也就是使用的 rsyslog 的默认模板，可以通过 man 来查看 logger 的其他用法。
![logger参数介绍.png](https://upload-images.jianshu.io/upload_images/13407176-461cca6761065cc5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 三、转储的日志
- 在本地的机器中每天都有成百上千条日志被写入文件中，更别说是服务器，每天都会有数十兆甚至更多的日志信息被写入文件中，如果是这样的话，每天看着我们的日志文件不断的膨胀，那岂不是要占用许多的空间，所以有个叫 logrotate 的东西诞生了。
- logrotate 程序是一个日志文件管理工具。用来把旧的日志文件删除，并创建新的日志文件。我们可以根据日志文件的大小，也可以根据其天数来切割日志、管理日志，这个过程又叫做“转储”。
- 大多数 Linux 发行版使用 logrotate 或 newsyslog 对日志进行管理。logrotate 程序不但可以压缩日志文件，减少存储空间，还可以将日志发送到指定 E-mail，方便管理员及时查看日志。显而易见，logrotate 是基于 CRON 来运行的，其脚本是 /etc/cron.daily/logrotate；同时我们可以在 /etc/logrotate 中找到其配置文件。
```
cat /etc/logrotate.conf
```
![logrotate.conf文件.png](https://upload-images.jianshu.io/upload_images/13407176-2752eb2af848e9a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 具体解释如下：
```
# see "man logrotate" for details  //可以查看帮助文档
# rotate log files weekly
weekly                             //设置每周转储一次(daily、weekly、monthly当然可以使用这些参数每天、星期，月 )
# keep 4 weeks worth of backlogs
rotate 4                           //最多转储4次
# create new (empty) log files after rotating old ones
create                             //当转储后文件不存在时创建它
# uncomment this if you want your log files compressed
compress                          //通过gzip压缩方式转储（nocompress可以不压缩）
# RPM packages drop log rotation information into this directory
include /etc/logrotate.d           //其他日志文件的转储方式配置文件，包含在该目录下
# no packages own wtmp -- we'll rotate them here
/var/log/wtmp {                    //设置/var/log/wtmp日志文件的转储参数
    monthly                        //每月转储
    create 0664 root utmp          //转储后文件不存在时创建它，文件所有者为root，所属组为utmp，对应的权限为0664
    rotate 1                       //最多转储一次
}
```
