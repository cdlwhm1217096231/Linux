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
