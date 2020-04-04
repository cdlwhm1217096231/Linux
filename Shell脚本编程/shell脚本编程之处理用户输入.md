### 技术交流QQ群:1027579432，欢迎你的加入！
### 本教程使用Linux发行版Centos7.0系统，请您注意~
#### 1.命令行参数
- bash shell提供了一些不同的方法来从用户处获得数据，包括命令行参数(添加在命令后的数据)、命令行选项(可修改命令行为的单个字母)以及直接从键盘读取输入的能力。
- 向shell脚本传递数据的最基本方法是使用命令行参数，命令行参数允许你在运行脚本时向命令行添加数据。例如 ./addem 10 30
##### 1.1 读取参数
- bash shell会将一些称为**位置参数**的特殊变量分配给输入到命令行中的所有参数，这也包括脚本的名称。**位置参数变量是标准的数字：$0是程序名，$1是第一个参数，$2是第二个参数，以此类推，$9是第九个参数**。如下例所示：
    ```
    #!/bin/bash


    var=1
    for (( n = 1; n <= $1; n++ ))
    do
        var=$[ $var * n ]
    done

    echo "The var of $1 is $var"

    # 结果
    [njust@njust tutorials]$ ./bar1.sh 5  # 注意命令行中的参数5
    The var of 5 is 120
    ```
- 可以在shell脚本中像使用普通变量一样使用$1变量，shell脚本会自动将命令行参数的值分配给变量，不需要你作任何处理。**如果需要输入更多的命令行参数，则每个参数之间都必须用空格分开**。如下例所示：
    ```
    #!/bin/bash

    res=$[ $1 * $2 ]

    echo "The first parameter is $1"
    echo "The second parameter is $2"
    echo "The final result is $res"

    # 结果
    [njust@njust tutorials]$ ./bar2.sh 3 5  # 输入更多命令行参数时，参数之间用空格进行分割！
    The first parameter is 3
    The second parameter is 5
    The final result is 15
    ```
- 在上面的例子中，用到的命令行参数都是数值，**也可以在命令行上使用文本字符串**。如下例所示：
    ```
    #!/bin/bash

    echo "Hello $1, glad to meet you."

    # 结果
    [njust@njust tutorials]$ ./bar3.sh Curry
    Hello Curry, glad to meet you.
    ```
- **shell将输入到命令行的字符串值传递给脚本，但碰到含有空格的文本字符串时就会出现问题，因为每个命令行参数都是用空格分隔的，所以shell会将空格当成两个值的分隔符。解决方法：对含有空格的文本字符串，必须要用引号**。如下例所示：
    ```
    [njust@njust tutorials]$ ./bar3.sh 'James Harden'
    Hello James Harden, glad to meet you.
    ```
- **当命令行参数不只9个时，在第九个变量后，必须在变量数字周围加上花括号{}，例如${10}**，如下例所示：
    ```
    #!/bin/bash


    res=$[ ${10} * ${11} ]

    echo "The tenth parameter is ${10}"
    echo "The eleventh parameter is ${11}"

    echo "The final result is $res"

    # 结果
    [njust@njust tutorials]$ ./bar4.sh 1 2 3 4 5 6 7 8 9 10 11
    The tenth parameter is 10
    The eleventh parameter is 11
    The final result is 110
    ```
##### 1.2 读取脚本名
- 可以使用$0参数获取shell在命令行启动的脚本名称，如下例所示：
    ```
    #!/bin/bash

    echo "The zero parameter is set to: $0"

    # 结果
    [njust@njust tutorials]$ bash bar5.sh   # 注意此处运行脚本的格式！！！
    The zero parameter is set to: bar5.sh
    ```
- 问题一：如果使用./bar5.sh方式运行脚本，命令会和脚本混在一起，出现在$0参数中。如下例所示：
    ```
    [njust@njust tutorials]$ ./bar5.sh 
    The zero parameter is set to: ./bar5.sh
    ```
- 问题二：当传给$0变量的实际字符串不仅仅是脚本名，而是完整的脚本路径时，变量$0就会使用整个路径。如下例所示：
    ```
    [njust@njust tutorials]$ bash /home/njust/tutorials/bar5.sh
    The zero parameter is set to: /home/njust/tutorials/bar5.sh
    ```
- 解决方法：为了把脚本的运行路径给剥离掉，同时，还要删除与脚本名混杂在一起的命令，**可以使用basename命令会返回不包含路径的脚本名**。如下例所示：
    ```
    #!/bin/bash

    name=$(basename $0)


    echo
    echo "The script name is:$name"

    # 结果
    [njust@njust tutorials]$ ./bar6.sh 

    The script name is:bar6.sh
    # 结果
    [njust@njust tutorials]$ bash bar6.sh 

    The script name is:bar6.sh
    ```
- **可以使用上述方法来编写基于脚本名执行不同功能的脚本**。如下例所示：
    ```
    #!/bin/bash

    name=$(basename $0)

    if [ $name = "addem" ]
    then
        res=$[ $1 + $2 ]
    elif [ $name = "multem" ]
    then    
        res=$[ $1 * $2 ]
    fi
    echo "The calculated value is:$res"

    # 通过复制文件创建addem
    [njust@njust tutorials]$ cp bar7.sh addem
    [njust@njust tutorials]$ chmod u+x bar7.sh 

    # 通过软链接创建multem
    [njust@njust tutorials]$ ln -s bar7.sh  multem

    # 查看创建的文件
    [njust@njust tutorials]$ ls -l *em
    -rwxrw-r--. 1 njust njust 180 4月   2 21:01 addem
    lrwxrwxrwx. 1 njust njust   7 4月   2 21:02 multem -> bar7.sh

    # 最终结果
    [njust@njust tutorials]$ ./addem 2 8
    The calculated value is:10
    [njust@njust tutorials]$ ./multem 2 8
    The calculated value is:16
    ```
##### 1.3 测试参数
- 在shell脚本中使用命令行参数时要小心，如果不加参数运行，可能会出现问题。如下例所示：
    ```
    [njust@njust tutorials]$ ./addem 4
    ./addem:行7: 4 +  : 语法错误: 期待操作数 （错误符号是 "+  "）
    The calculated value is:
    ```
- 当脚本认为参数变量中会有数据而实际上并没有时，脚本很可能会产生错误消息。这样的脚本写法并不好，**在使用参数前一定要检查其中是否存在数据**。如下例所示：
    ```
    #!/bin/bash

    if [ -n "$1" ]  # -n测试来检查命令行参数$1中是否有数据
    then
        echo "Hello $1,glad to meet you."
    else
        echo "Sorry,you did not identify yourself."
    fi

    # 结果
    [njust@njust tutorials]$ ./bar8.sh Curry
    Hello Curry,glad to meet you.

    [njust@njust tutorials]$ ./bar8.sh
    Sorry,you did not identify yourself.
    ```
#### 2.特殊参数变量
- 在bash shell中有些特殊变量，它们会记录命令行参数。具体如下介绍：
##### 2.1 参数统计
- 在脚本中使用命令行参数之前应该检查一下命令行参数，可以统计一下命令行中输入了多少个参数，无需测试每个参数。bash shell提供了特殊变量$#，它**含有脚本运行时携带的命令行参数的个数，可以在脚本中任何地方使用这个特殊变量，与普通变量一样**。如下例所示：
    ```
    #!/bin/bash

    echo "There were $# parameters supplied."

    # 结果
    [njust@njust tutorials]$ ./bar9.sh 
    There were 0 parameters supplied.

    [njust@njust tutorials]$ ./bar9.sh 1 2 3
    There were 3 parameters supplied.
    ```
- 在使用参数前测试参数的总和，如下例所示：
    ```
    #!/bin/bash

    if [ $# -ne 2 ]  # -ne测试命令行参数数量，如果参数数量不对，就会显示一条错误消息告知脚本的正确用法。
    then
        echo "Usage: bar10.sh a b"
    else
        res=$[ $1 + $2 ]
        echo "result is $res"
    fi

    # 结果
    [njust@njust tutorials]$ ./bar10.sh 
    Usage: bar10.sh a b

    [njust@njust tutorials]$ ./bar10.sh 2 
    Usage: bar10.sh a b

    [njust@njust tutorials]$ ./bar10.sh 2 3
    result is 5
    ```
- $#变量含有参数的总和，因此变量${!#}就代表了命令行中最后一个参数。注意：**当命令行上没有任何参数时，$#的值为0，params变量的值也一样，但${!#}变量会返回命令行的脚本名**，如下例所示：
    ```
    #!/bin/bash

    params=$#

    echo "The last parameter is $params."
    echo "The last parameter is ${!#}"

    # 结果
    [njust@njust tutorials]$ ./bar11.sh 12 3 42 
    The last parameter is 3.
    The last parameter is 42

    [njust@njust tutorials]$ ./bar11.sh 
    The last parameter is 0.
    The last parameter is ./bar11.sh
    ```
##### 2.2 抓取所有的数据
- 有时候需要抓取命令行上所有的参数，此时不需要先用$#变量来判断命令行上总的参数，然后再进行遍历。可以使用**$\*和$@变量来访问所有的变量，这两个变量都能够在单个变量中存储所有的命令行参数**。
    - $\*变量会将命令行上提供的所有参数当作一个单词保存，这个单词包含了命令行中出现的每个参数值，基本上$\*变量会将这些参数视为一个整体；
    - $@变量会将命令行上提供的所有参数当成同一个字符串中的多个独立的单词，这个就可以通过遍历所有的参数值，得到每个参数。**通常通过for命令来实现**。
- 两个变量的具体功能，如下例所示：
    ```
    [njust@njust tutorials]$ ./bar12.sh  1 3 4 5 2
    Using the $* method: 1 3 4 5 2
    Using the $@ method: 1 3 4 5 2
    ```
- 两个变量的具体区别，如下例所示：
    ```
    #!/bin/bash

    count=1

    for param in "$*"
    do
        echo "\$* parameter #$count = $param"
        count=$[ $count + 1 ]
    done

    echo
    echo

    cnt=1
    for param in "$@"
    do
        echo "\$@ parameter #$cnt = $param"
        cnt=$[ $cnt + 1 ]
    done

    # 结果
    [njust@njust tutorials]$ ./bar13.sh Curry Harden Durant James
    $* parameter #1 = Curry Harden Durant James


    $@ parameter #1 = Curry
    $@ parameter #2 = Harden
    $@ parameter #3 = Durant
    $@ parameter #4 = James
    ```
#### 3.移动变量
- bash shell的shift命令能够用来操作命令行参数，shift命令会根据它们的相对位置来移动命令行参数。**默认情况下，它会将每个参数变量向左移动一个位置**。所以，变量$3的值会移动$2，变量$2的值会移到$1中，**变量$1的值会被删除**。但是，**变量$0的值不会被改变，因为它是程序名**。通过测试第一个参数值的长度执行了一个while循环，当第一个参数的长度为零时，循环结束。测试完第一个参数后，shift命令会将所有参数的位置移动一个位置，如下例所示：
    ```
    #!/bin/bash


    count=1
    while [ -n "$1" ]
    do
        echo "Parameter #$count = $1"
        count=$[ $count + 1 ]
        shift
    done

    # 结果
    [njust@njust tutorials]$ ./bar14.sh Curry Harder Durant James
    Parameter #1 = Curry
    Parameter #2 = Harder
    Parameter #3 = Durant
    Parameter #4 = James
    ```
- 也可以一次性移动多个位置，只需要给shift命令提供一个参数，指明要移动的位置数就行。如下例所示：
    ```
    #!/bin/bash

    echo "The original parameters: $*"
    shift 2
    echo "Here's the new first parameter: $1"

    # 结果
    [njust@njust tutorials]$ ./bar15.sh  3 4 253 5 38
    The original parameters: 3 4 253 5 38
    Here's the new first parameter: 253
    ```
#### 4.处理选项
- 选项是跟在单折线后面的单个字母，它能改变命令的行为。下面将介绍3种在脚本中处理选项的方法。
##### 4.1 查找选项
- **处理简单选项**：在提取每个单独参数时，用case语句来判断某个参数是否为选项。如下例所示：
    ```
    #!/bin/bash


    while [ -n "$1" ]
    do
        case "$1" in
            -a) echo "Found the -a option";;
            -b) echo "Found the -b option";;
            -c) echo "Found the -c option";;
            *) echo "$1 is not an option";;
        esac
        shift
    done

    # 结果
    [njust@njust tutorials]$ ./bar16.sh -a -b -c -d
    Found the -a option
    Found the -b option
    Found the -c option
    -d is not an option
    ```
- **分离参数和选项**：在shell脚本中同时使用选项和参数的情况，Linux中处理这个问题的标准方法是用特殊字符来将两者分开，该字符会告诉脚本何时选项结束以及普通参数何时开始。对Linux来说，这个特殊字符是\-\-，shell会用双破折线来表明选项列表已经结束。在双破折线后，脚本就可以放心将剩下的命令行参数当作参数，而不是选项。如下例所示：
    ```
    #!/bin/bash

    while [ -n "$1" ]
    do
        case "$1" in
            -a) echo "Found the -a option";;
            -b) echo "Found the -b option";;
            -c) echo "Found the -c option";;
            --) shift
                break;;
            *) echo "$1 is not an option";;
        esac
        shift
    done

    count=1

    for param in $@
    do
        echo "Parameter #$count: $param"
        count=$[ count + 1 ]
    done

    # 结果
    [njust@njust tutorials]$ ./bar17.sh -c -a -b -- test1 test2 test3
    Found the -c option
    Found the -a option
    Found the -b option
    Parameter #1: test1
    Parameter #2: test2
    Parameter #3: test3
    ```
- **处理带值的选项**：有些选项会带上一个额外的参数值，如./bar666.sh -a test1 -b -c -d test2。当命令行选项要求额外的参数时，脚本必须能检测到并正确处理。如下例所示：
    ```
    #!/bin/bash


    while [ -n "$1" ]
    do
        case "$1" in
            -a) echo "Found the -a option";;
            -b) param="$2"
                echo "Found the -b option,with parameter value $param."
                shift;;
            -c) echo "Found the -c option";;
            --) shift
                break;;
            *) echo "$1 is not an option";;
        esac
        shift
    done


    count=1
    for param in "$@"
    do
        echo "Parameter #$count: $param"
        count=$[ $count + 1 ]
    done

    # 结果
    [njust@njust tutorials]$ ./bar19.sh -a -b test1 -f -- demo1 demo2
    Found the -a option
    Found the -b option,with parameter value test1.
    -f is not an option
    Parameter #1: demo1
    Parameter #2: demo2
    ```
##### 4.2 使用getopt命令
- getopt命令是一个在处理命令行选项和参数时非常方便的工具，它能识别命令行参数，从而在脚本中解析它们时更方便。
- **命令格式**：getopt命令可以接受一系列任意形式的命令行选项和参数，并自动转换成适当的格式，具体命令格式如下所示：
    ```
    getopt optstring parameters
    ```
- optstring定义了命令行有效的选项字母，还定义了哪些选项字母需要参数值。**optstring中列出了要在脚本中用到的每个命令行选项字母。然后，在每个需要参数值的选项字母后加一个冒号**。注意：getopt命令有一个更高级的版本即getopts，注意两者的区别。具体实例如下所示：
    ```
    [njust@njust tutorials]$ getopt ab:cd -a -b test1 -cd test2 test3
    -a -b test1 -c -d -- test2 test3
    ```
- 上述实例中，optstring定义了四个有效选项字母：a、b、c、d。字母b后面的冒号表示b选项需要一个参数值。**注意：getopt命令会将-cd选项分成两个单独的选项，并插入双破折线来分隔行中的额外参数**。
- 如果指定了一个不在abcd选项中的参数，默认情况下，getopt命令会产生一条错误的信息。如下所示：
    ```
    [njust@njust tutorials]$ getopt ab:cd -a -b test1 -cdf test2 test3
    getopt：无效选项 -- f
    -a -b test1 -c -d -- test2 test3
    ```
- 如果需要忽略这条错误信息，可以在命令后加上-q选项，如下所示：
    ```
    [njust@njust tutorials]$ getopt -q ab:cd -a -b test1 -cdf test2 test3
    -a -b 'test1' -c -d -- 'test2' 'test3'
    ```
- **在脚本中使用getopt**：用getopt命令生成的格式后的版本来替换自己已有的命令行选项和参数，用set命令能够做到。set命令使用的方法如下：
    ```
    set -- $(getopt -q ab:cd "$@")
    ```
- 利用上述方法，可以帮我们处理命令行参数的脚本。如下例所示：
    ```
    #!/bin/bash


    set -- $(getopt -q ab:cd "$@")   # 注意此条语句！！！

    while [ -n "$1" ]
    do
        case "$1" in
            -a) echo "Found the -a option";;
            -b) param="$2"
                echo "Found the -b option,with parameter value $param."
                shift;;
            -c) echo "Found the -c option";;
            --) shift
                break;;
            *) echo "$1 is not an option";;
        esac
        shift
    done


    count=1
    for param in "$@"
    do
        echo "Parameter #$count: $param"
        count=$[ $count + 1 ]
    done

    # 结果
    [njust@njust tutorials]$ ./bar20.sh -ac  # 合并选项情况下也能处理!
    Found the -a option
    Found the -c option
    ```
- **但是，getopt命令也存在一个小问题。它并不擅长处理带空格和引号的参数值。它会将空格当成参数分隔符，而不是根据双引号将两者看成一个参数，解决方法是使用更高级的getopts命令**。
    ```
    [njust@njust tutorials]$ ./bar20.sh -a -b test1 -c "test2 test3" test4
    Found the -a option
    Found the -b option,with parameter value 'test1'.
    Found the -c option
    Parameter #1: 'test2
    Parameter #2: test3'
    Parameter #3: 'test4'
    ```
##### 4.3 使用更高级的getopts命令
- getopts命令是shell中的内建命令，它比getopt命令多了一些扩展功能。getopt命令将命令行上选项和参数处理后只生成一个输出，而getopts命令能够和已有的shell参数变量配合使用。每次调用它时，它一次只处理命令行上检测到的一个参数，处理完所有的参数后，它会退出并返回一个大于0的退出状态码，这让它非常适合用解析命令行所有参数的循环中。getopts命令的格式如下：
    ```
    getopts optstring variable
    ```
- optstring值类似于getopt命令中的那个，**getopts命令将当前参数保存在命令行中定义的variable中**。getopts命令会用到两个环境变量，如果选项需要跟一个参数值，OPTARG环境变量就会保存这个值，OPTIND环境变量保存了参数列表中getopts正在处理的参数位置，这样你就能在处理完选项后继续处理其他命令行参数了。如下例所示：
    ```
    #!/bin/bash


    while getopts :ab:c opt
    do
        case "$opt" in
            a) echo "Found the -a option";; # 注意：getopts命令在解析命令行选项时会移除开头的单破折线，所以在case定义中不用单破折线！
            b) echo "Found the -b option,with value $OPTARG";;
            c) echo "Found the -c option";;
            *) echo "Unknown option: $opt";;
        esac
    done

    # 结果
    [njust@njust tutorials]$ ./bar21.sh  -ab test1 -c
    Found the -a option
    Found the -b option,with value test1
    Found the -c option

    ```
- getopts命令的优点之一是可以在参数值中包含空格，如下例所示：
    ```
    [njust@njust tutorials]$ ./bar21.sh  -b "test1 test2" -a
    Found the -b option,with value test1 test2
    Found the -a option
    ```
getopts命令的另一个优点是将选项字母和参数值放在一起使用，而不用加空格。如下例所示：
    ```
    [njust@njust tutorials]$ ./bar21.sh  -abdemo1
    Found the -a option
    Found the -b option,with value demo1
    ```
- 此外，getopts命令还可以将命令行上找到的所有未定义的选项统一输出为问号，如下例所示：
    ```
    [njust@njust tutorials]$ ./bar21.sh  -acdf
    Found the -a option
    Found the -c option
    Unknown option: ?
    Unknown option: ?
    ```
- getopts命令知道何时停止处理选项，并将参数留给你处理。在getopts处理每个选项时，它会将OPTIND环境变量值加1，在getopts完成处理时，使用shift命令和OPTIND值来移动参数。如下例所示：
    ```
    #!/bin/bash


    while getopts :ab:cd opt
    do
        case "$opt" in
            a) echo "Found the -a option";;
            b) echo "Found the -b option,with value $OPTARG";;
            c) echo "Found the -c option";;
            d) echo "Found the -d option";;
            *) echo "Unknown option: $opt";;
        esac
    done

    shift $[ $OPTIND - 1 ]

    count=1
    for param in "$@"
    do
        echo "Parameter $count:$param"
        count=$[ $count + 1 ]
    done

    # 结果
    [njust@njust tutorials]$ ./bar22.sh -a -b test1 -d test2 test3 test4
    Found the -a option
    Found the -b option,with value test1
    Found the -d option
    Parameter 1:test2
    Parameter 2:test3
    Parameter 3:test4
    ```
#### 5.将选项标准化
- 下面是Linux中常用到的一些命令行选项的含义。
    ```
    选项                               含义
    -a                             显示所有对象
    -c                             生成一个计数
    -d                             指定一个目录
    -e                             扩展一个对象
    -f                             指定读入数据的文件
    -h                             显示命令的帮助信息
    -i                             忽略文本大小写
    -l                             产生输出的长格式版本
    -n                             使用非交互模式
    -o                             将所有输出重定向到指定的输出文件
    -q                             以安静模式运行
    -r                             递归地处理目录和文件
    -s                             以安静模式运行
    -v                             生成详细输出
    -x                             排除某个对象
    -y                             对所有问题回答yes
    ```
#### 6.获得用户输入
- 尽管命令行选项和参数是从脚本用户处获得输入的一种重要方式，但是有时候脚本的交互性还需要更强一些。例如在运行脚本时问一个问题，并等待运行脚本的人来回答，**bash shell为此提供了read命令**。
##### 6.1 基本的读取
- read命令从标准输入或另一个文件描述符中接收输入。在收到输入后，read命令会将数据放进一个变量，如下例所示：
    ```
    #!/bin/bash


    echo -n "Please Enter your name: "  # -n选项不会在字符串末尾输出换行符，允许脚本用户紧跟其后输入数据，而不是在下一行输入！
    read name
    echo "Hello $name,welcome to my home."

    # 结果
    [njust@njust tutorials]$ ./bar23.sh 
    Please Enter your name: Curry   # 自己输入的！
    Hello Curry,welcome to my home.
    ```
- read命令包含了-p选项，它允许你直接在read命令行指定提示符，如下例所示：
    ```
    #!/bin/bash


    read -p "Please Enter your age: " age
    days=$[ $age * 365 ]
    echo "That makes you over $days days old"

    # 结果
    [njust@njust tutorials]$ ./bar24.sh 
    Please Enter your age: 28
    That makes you over 10220 days old
    ```
- 也可以在read命令行中不指定变量，read命令会将它收到的任何数据都放进特殊环境变量REPLY中，REPLY环境变量会保存输入的所有数据，可以在shell脚本中像其他变量一样使用。如下例所示：
    ```
    #!/bin/bash


    read -p "Enter your name: "
    echo "Hello $REPLY,welcome to NewYork."

    # 结果
    [njust@njust tutorials]$ ./bar25.sh 
    Enter your name: Stephen Curry
    Hello Stephen Curry,welcome to NewYork.
    ```
##### 6.2 超时
- 使用read命令时一定要注意，脚本很可能会一直等待用户的输入。如果不管是否有数据输入，脚本都必须继续执行，可以使用-t选项来指定一个计数器。-t选项指定了read命令等待输入的秒数，当计数器超时后，read命令会返回一个非零退出状态码，如下例所示：
    ```
    #!/bin/bash


    if read -t 5 -p "Please enter your name: " name
    then
        echo "Hello $name,welcome to NewYork."
    else
        echo 
        echo "Sorry, it's wrong!"
    fi

    # 结果
    [njust@njust tutorials]$ ./bar26.sh 
    Please enter your name: 
    Sorry, it's wrong!
    ```
- 也可以不对输入过程计时，而是让read命令来统计输入的字符数，当输入的字符达到预设的字符数时，就自动退出，将输入的数据赋值给变量。如下例所示：
    ```
    #!/bin/bash


    read -n1 -p "Do you want to continue [Y/N]? " answer
    case $answer in
        Y | y) echo
                echo "Fine,continue on...";;
        N | n) echo
                echo "Ok,goodbye"
                exit;;
    esac
    echo "This is the end of the script"


    # 结果
    [njust@njust tutorials]$ ./bar27.sh 
    Do you want to continue [Y/N]? Y
    Fine,continue on...
    This is the end of the script
    ```
- 上述例子中，-n选项与值1一起使用，它告诉read命令在接收单个字符后退出。只要按下单个字符后，read命令就会接收输入并将它传给变量，无需按下回车键。
##### 6.3 隐藏方式读取
- 有时候需要从用户输入处得到输入，但又不能在屏幕中显示输入。其中典型的例子就是输入的密码，此外还有很多其他需要隐藏的数据类型。-s选项可以避免在read命令中输入的数据出现在屏幕上，如下例所示：
    ```
    #!/bin/bash


    read -s -p "Enter you password: " passwd
    echo
    echo "Is your password really $passwd? "

    # 结果
    [njust@njust tutorials]$ ./bar28.sh 
    Enter you password: 
    Is your password really 12345? 
    ```
##### 6.4 从文件中读取
- 可以用read命令来读取Linux系统上文件中保存的数据，每次调用read命令，它都会从文件中读取一行文本。当文本中没有内容时，read命令会退出并返回非零退出状态码。注意：**最难的部分是将文件中的数据传给read命令，最常见的方法是对文件使用cat命令，将结果通过管道直接传给含有read命令的while命令**。如下例所示：
    ```
    #!/bin/bash

    count=1

    cat demodemo | while read line
    do
        echo "Line $count: $line"
        count=$[ $count + 1 ]
    done
    echo "Finished processing the file"

    # 结果
    [njust@njust tutorials]$ ./bar29.sh 
    Line 1: The quick brown dog jumps over the lazy fox.
    Line 2: This is a test, this is only a test.
    Line 3: O Romeo, Romeo!Wherefore art thou Romeo?
    Finished processing the file
    ```
#### 7.资料下载
- [笔记，欢迎star,follow,fork......](https://github.com/cdlwhm1217096231/Linux/tree/master/Shell%E8%84%9A%E6%9C%AC%E7%BC%96%E7%A8%8B)