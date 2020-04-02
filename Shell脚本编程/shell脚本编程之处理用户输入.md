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