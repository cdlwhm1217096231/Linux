### 技术交流QQ群:1027579432，欢迎你的加入！
#### 1.基本的脚本函数
- 脚本函数出现的目的：为了解决大型处理过程中，需要将相同的重复代码封装起来，提高代码的复用性。
- 函数是一个脚本代码块，你可以为其命名并在代码的任何位置重用。**要在脚本中使用该代码块时，只要使用所起的函数名即可(即调用函数)**。
#### 2.创建函数
- **有两种方法可以在bash shell脚本中创建函数**。
    - 方法1：采用关键字function，后面跟自定义的函数名。形式如下所示：
        - 脚本中定义的每个函数都必须有唯一一个函数名；
        - commands是构成函数的一条或多条shell命令；
        - 调用函数时，bash shell会按命令在函数中出现的先后顺序依次执行；
        ```
        function 函数名 {
            commands
        }
        ```
    - 方法2：更接近于其他编程语言中定义的函数形式，具体格式如下所示：
        - 函数名后的空括号表示正在定义的是一个函数；
        - 方法2这种格式的命令规则和之前定义shell脚本函数的格式一样。
        ```
        name() {
            commands
        }
        ```
#### 3.使用函数
- **要在脚本中使用函数时，只需要像其他shell命令一样，在行中指定函数名即可**。例如如下所示：
    ```
    #!/bin/bash

    function func1 {
        echo "This is an example of a function"
    }

    count=1

    while [ $count -le 5 ]
    do
        # 下面一句是调用函数func1
        func1
        count=$[ $count + 1 ]
    done

    echo "This is the end of the loop"
    func1
    echo "Now this is the end of the script"

    # 结果
    [njust@njust tutorials]$ ./func1 
    This is an example of a function
    This is an example of a function
    This is an example of a function
    This is an example of a function
    This is an example of a function
    This is the end of the loop
    This is an example of a function
    ```
- 不能在函数定义前，调用该函数。否则会出现像func2这样的错误：
    ```
    #!/bin/bash

    count=1
    echo "This line comes before the function definition"

    function func1 {
        echo "This is an example of a function1"
    }

    while [ $count -le 5 ]
    do
        func1
        count=[ $count + 1 ]
    done

    echo "This is the end of the loop"


    func2
    echo "Now this is the end of the script"

    function func2 {
        echo "This is an example of a function2"
    }

    # 结果
    [njust@njust tutorials]$ ./func2
    This line comes before the function definition
    This is an example of a function1
    ./func2:行13: 1: 未找到命令
    ```
- 注意：函数名必须唯一，否则后面定义的同名函数会覆盖原先定义的函数，如下所示。首先定义一个函数func1，然后执行func1；接着定义一个同名的函数func1，在调用func1。此时调用的func1是第二次定义的func1，它会将第一次定义的func1函数覆盖。
    ```
    #!/bin/bash


    function func1 {
        echo "This is the first definition of the function name"
    }


    func1

    function func1 {
        echo "This is a repeat of the same function name"
    }

    func1


    echo "This is the end of the script."

    # 结果
    [njust@njust tutorials]$ ./func3 
    This is the first definition of the function name
    This is a repeat of the same function name
    This is the end of the script.
    ```
#### 4.返回值
- **bash shell会把函数当成一个小型的脚本，函数运行结束后会返回一个退出状态码，有三种方法来为函数生成退出状态码**。
##### 4.1 默认退出状态码
- **默认情况下**，函数的退出状态码是函数中最后一条命令返回的退出状态码。在函数执行结束后，可以使用标准变量\$?来确定函数的退出状态码。如下所示：
    ```
    #!/bin/bash


    func4() {
        echo "trying to display a non-existent file"
        ls -l badfilesd
    }

    echo "testing the function:"
    func4
    echo "The exit status is: $?"

    # 结果
    [njust@njust tutorials]$ ./func4
    testing the function:
    trying to display a non-existent file
    ls: 无法访问badfilesd: 没有那个文件或目录
    The exit status is: 2

    ```
- 因为上例中，函数中的**最后一条指令没有成功运行**，因此函数的退出状态码为2。修改上面的例子如下所示：
    ```
    #!/bin/bash


    func4() {
        ls -l badfiles
        echo "trying to display a non-existent file"
    }

    echo "testing the function:"
    func4
    echo "The exit status is: $?"

    # 结果
    [njust@njust tutorials]$ ./func5
    testing the function:
    ls: 无法访问badfiles: 没有那个文件或目录
    trying to display a non-existent file
    The exit status is: 0
    [njust@njust tutorials]$ cat func5
    ```
- 修改过后的例子中，由于函数**最后一条语句echo成功运行，于是此函数的退出状态码是0，尽管函数中有一条命令没有正常运行**。但是，使用函数的默认退出状态码是很危险的，因此有下面两种方法解决。
##### 4.2 使用return命令
- bash shell使用return命令来退出函数并返回特定的退出状态码。**return命令允许运行指定一个整数值来定义函数的退出状态码**。如下例所示：
    ```
    #!/bin/bash

    function func6 {
        read -p "Enter a value: " value
        echo "doubling the value"
        return $[ $value * 2 ]
    }


    func6

    echo "The new value is $?"

    # 结果
    [njust@njust tutorials]$ ./func6
    Enter a value: 4
    doubling the value
    The new value is 8
    ```
- 上例中，func6函数将\$value变量中用户输入的值翻倍，return命令用来返回结果。**注意：当使用这样方法从函数中返回值时，要记住下面的两条技巧**：
    - a.函数一结束就取返回值即**\$?变量会返回执行的最后一条命令的退出状态码**。
    - b.退出状态码必须在0~255之间即**由于退出状态码必须小于256，因此函数的结果必须生成小于256的整数值，任何大于256的值都会产生一个错误值**。
##### 4.3 使用函数输出
- 上一种方法中，如果返回较大的整数值或字符串值时，就不能使用return命令返回函数的退出状态码了。**因此，可以使用变量来保存函数的输出。使用这种技术来获得任何类型的函数输出，并将其保存在变量中**。如下例所示：
    ```
    #!/bin/bash


    function func7 {
        read -p "Enter a value: " value
        echo $[ $value * 2 ]
    }

    result=$(func7)  # 此句是重点，使用一个变量result来保存函数func7需要返回的值

    echo "The new value is $result."

    # 结果
    [njust@njust tutorials]$ ./func7
    Enter a value: 1000
    The new value is 2000.
    ```
#### 5.在函数中使用变量
- 在函数中使用变量时，需要注意它们的定义方式和处理方式，这是shell脚本中常见错误的根源。
##### 5.1 向函数传递参数
- 由于bash shell会将函数当作小型脚本来对待，因此可以像普通脚本那样向函数传递参数。**在脚本中指定函数时，必须将参数和函数放在同一行**，如下所示：
    ```
    func1 $value1 10
    ```
- 函数可以使用标准的参数环境变量来表示命令行上传给函数的参数，\$0表示函数名称，\$1、\$2等表示传递给函数的一般函数，\$#表示传递给函数的参数总数。**在脚本中指定函数时，必须将函数和参数放在同一行**，如下所示：
    ```
    #!/bin/bash

    function adddemo {
    if [ $# -eq 0 ] || [ $# -gt 2 ]
    then
        echo -1
    elif [ $# -eq 1 ]
    then
        echo $[ $1 + $1 ]
    else
        echo $[ $1 + $2 ]
    fi
    }

    echo -n "Adding 10 and 15: "
    value=$(adddemo 10 15)
    echo $value

    echo -n "Let's try adding just one number:"
    value=$(adddemo 10)
    echo $value

    echo -n "Finally,try adding three numbers:"
    value=$(adddemo 10 15 20)
    echo $value

    # 结果
    [njust@njust tutorials]$ ./func8
    Adding 10 and 15: 25
    Let's try adding just one number:20
    Finally,try adding three numbers:-1
    ```
- 由于上述函数使用特殊参数环境变量作为自己的参数值，因此它**无法直接获取脚本在命令行中的参数值**，如下所示：
    ```
    #!/bin/bash

    function badfunc {
    echo $[ $1 * $2 ]
    }

    if [ $# -eq 2 ]
    then
        value=$(badfunc)
        echo "The result is $value"
    else
        echo "Usage:badtest a b"
    fi

    # 运行失败的结果
    [njust@njust tutorials]$ ./func9
    Usage:badtest a b
    [njust@njust tutorials]$ ./func9 10 14
    ./func9:行4: *  : 语法错误: 期待操作数 （错误符号是 "*  "）
    The result is 
    ```
- 尽管函数badfunc使用了\$1和\$2变量，但是它们和脚本主体中的\$1和\$2并不相同。**要在函数中使用这些值，必须在调用函数时手动将它们传过去**。如下所示：
    ```
    #!/bin/bash

    function badfunc {
    echo $[ $1 * $2 ]
    }

    if [ $# -eq 2 ]
    then
        value=$(badfunc $1 $2)   # 调用函数时，需要将参数手动传过去！
        echo "The result is $value"
    else
        echo "Usage:badtest a b"
    fi

    # 结果
    [njust@njust tutorials]$ ./func10
    Usage:badtest a b
    [njust@njust tutorials]$ ./func10 23 9
    The result is 207
    ```
##### 5.2 在函数中处理变量
- **函数定义的变量与普通变量的作用域不同，即对脚本的其他部分而言，它们是隐藏的**。函数使用两种类型的变量：
    - 全局变量
    - 局部变量
- **全局变量**：全局变量是在shell整个脚本中**任何地方**都有效的变量。**默认情况下**，在脚本中定义的任何变量都是全局变量。在函数外定义的变量可以在函数内部正常访问。如下所示：
    ```
    #!/bin/bash


    function global_demo {
        value=$[ $value * 2 ]
    }

    read -p "Enter a value:" value  # 手动输入全局变量value!

    global_demo
    echo "The new value is: $value"

    # 结果
    [njust@njust tutorials]$ ./func11
    Enter a value:12
    The new value is: 24
    ```
- **局部变量**：**函数内部使用的任何变量都可以被声明成局部变量，只需要在变量声明前加上local关键字即可**。也可以在变量赋值语句中使用local关键字，如下所示：
    ```
    local temp=$[ $value + 5 ]
    ```
- local关键字保证了变量**只局限**在该函数中。如果脚本中在该函数之外有同样的名字变量，那么shell会保持这两个变量的值是分离的。如下所示：
    ```
    #!/bin/bash


    function func12 {
        local temp=$[ $value + 5 ]  # 此处的temp是局部变量
        result=$[ $temp * 2 ]
    }


    temp=4  # 此处的temp是全局变量
    value=6


    func12


    echo "The result is $result"

    if [ $temp -gt $value ]  # 此处的temp是全局变量
    then
    echo "temp is larger"
    else
    echo "temp is smaller"
    fi

    # 结果
    [njust@njust tutorials]$ ./func12
    The result is 22
    temp is smaller
    ```
#### 6.数组变量和函数
##### 6.1 向函数传数组参数
- 将数组变量当作单个参数传递的话，它会不起作用。如果将数组变量myarray作为函数参数，函数只会取出数组变量的第一个值1，如下所示：
    ```
    #!/bin/bash


    function func13 {
        echo "The parameters are: $@"
        thisarray=$1
        echo "The received array is ${thisarray[*]}"
    }

    myarray=(1 2 3 4 5)
    echo "The original array is: ${myarray[*]}"


    func13 $myarray

    # 结果
    [njust@njust tutorials]$ ./func13
    The original array is: 1 2 3 4 5
    The parameters are: 1
    The received array is 1
    ```
- 要解决上述问题，**必须将该数组变量的值分解成单个值，然后将这些值作为函数参数使用**。在函数内部，可以将所有的参数重新组合成一个变量。如下所示：
    ```
    #!/bin/bash


    function func13 {
        local newarray
        newarray=($(echo "$@"))
        echo "The new array value is: ${newarray[*]}"
    }

    myarray=(1 2 3 4 5)
    echo "The original array is: ${myarray[*]}"


    func13 ${myarray[*]}

    # 结果
    [njust@njust tutorials]$ ./func14
    The original array is: 1 2 3 4 5
    The new array value is: 1 2 3 4 5
    ```
- 上述改进后的方法中，用\$myarray变量来保存所有的数组元素，然后将它们都放在函数的命令行上。该函数随后从命令行中重建数组变量newarray。**在函数内部，数组仍然可以像其他数组一样使用**。如下所示：
    ```
    #!/bin/bash


    function addarray {
        local sum=0
        local newarray
        newarray=($(echo "$@"))
        for value in ${newarray[*]}
        do
            sum=$[ $sum + $value ]
        done
        echo "The finally result is: $sum"
    }

    myarray=(1 2 3 4 5)
    echo "The original array is: ${myarray[*]}"
    arg1=$(echo ${myarray[*]})

    result=$(addarray $arg1)
    echo $result

    # 结果
    [njust@njust tutorials]$ ./func15
    The original array is: 1 2 3 4 5
    The finally result is: 15
    ```
##### 6.2 从函数返回数组
- 函数用echo语句来按正确顺序输出单个数组值，然后脚本再将它们重新放入一个新的数组变量中。如下所示：
    ```
    #!/bin/bash


    function array_demo {
        local original_array
        local new_array
        local element
        local i
        original_array=($(echo "$@"))
        new_array=($(echo "$@"))
        element=$[ $# - 1 ]
        for (( i = 0; i <= $element; i++ ))
        {
            new_array[$i]=$[ ${original_array[$i]} * 2 ]
        }
        
        echo ${new_array[*]}
    }


    myarray=(1 2 3 4 5)
    echo "The original array is: ${myarray[*]}"
    arg1=$(echo ${myarray[*]})

    result=($(array_demo $arg1))

    echo "The finally result is: ${result[*]}"

    # 结果
    [njust@njust tutorials]$ ./func16
    The original array is: 1 2 3 4 5
    The finally result is: 2 4 6 8 10
    ```
#### 7.函数递归
- 函数可以调用自己来得到结果，通常递归函数都有一个最终可以迭代到的基准值。如下例中的阶乘示例程序：
    ```
    #!/bin/bash


    function fib {
        if [ $1 -eq 1 ]
        then
            echo 1
        else 
            local temp=$[ $1 - 1 ]
            local result=$(fib $temp)
            echo $[ $result * $1 ]
        fi
    }


    read -p "Enter value: " value
    result=$(fib $value)

    echo "The fib of $value is: $result"

    # 结果
    [njust@njust tutorials]$ ./func17
    Enter value: 5
    The fib of 5 is: 120
    ```
#### 8.创建库
- 假设需要在多个脚本中使用同一段代码，此时为了使用同一段相同的代码而在每个脚本中都定义同样的函数太麻烦了。**bash shell允许你创建函数库文件，然后在脚本中引用该库文件即可**。该方法的第一步是**创建一个包含脚本中所需函数的公用库文件**，如下例所示有个名为myfuncs的库文件，它定义了三个简单的函数：
    ```
    #!/bin/bash


    function addem {
        echo $[ $1 + $2 ]
    }

    function multem {
        echo $[ $1 * $2 ]
    }

    function divem {
        if [ $2 -ne 0 ]
        then
            echo $[ $1 / $2 ]
        else
            echo -1
        fi
    }
    ```
- **shell函数仅在定义它的shell会话内有效**，如果在shell命令行的提示符下运行myfuncs脚本，shell会创建一个新的shell并在其中运行这个脚本。它会为新的shell定义这三个函数。**但当你运行另外一个要用到这些函数的脚本时，它们是无法使用的**。如果你想像普通脚本文件那样运行库文件，函数并不会出现在脚本中。如下例所示：
    ```
    #!/bin/bash

    ./myfuncs

    result=$(addem 10 15)
    echo "The result is $result."

    # 错误结果
    [njust@njust tutorials]$ ./badlibs 
    ./badlibs:行5: addem: 未找到命令
    The result is .
    ```
- **使用函数库的关键在于source命令**。source命令会在当前shell上下文中执行命令，而不是创建一个新的shell。**可以使用source命令来在shell脚本中运行库文件脚本**，这样脚本就可以使用库文件中的函数了。**source命令有个快捷的别名，称为点操作符。要在shell脚本中运行myfuncs库文件，只需添加. ./myfuncs即可**。下例中假设**myfuncs库文件与shell脚本位于同一目录下。如果不是这种情况，需要使用相应路径访问myfuncs文件**。如下例所示：
    ```
    #!/bin/bash

    . ./myfuncs

    value1=10
    value2=5


    result1=$(addem $value1 $value2)
    result2=$(multem $value1 $value2)
    result3=$(divem $value1 $value2)

    echo "Add result is $result1"
    echo "Multiply result is $result2"
    echo "Division result is $result3"

    # 结果
    [njust@njust tutorials]$ ./func18
    Add result is 15
    Multiply result is 50
    Division result is 2
    ```
#### 9.在命令行上使用函数
- 有时候也很有必要在命令行界面的提示符中直接使用这些函数。和shell脚本中将脚本函数当作命令使用一样，在命令行界面中也可以这样做。**因为一旦在shell中定义了函数，就可以在整个系统中使用，不用担心脚本是不是在PATH环境变量中。重点在于如何让shell识别这些函数，有几种方法可以实现**。
##### 9.1 在命令行上创建函数
- 在命令行上直接定义一个函数，有两种方法。注意：**如果所起的函数名与内建的shell命令或另一个命令相同，函数会覆盖原来的命令**。
    - a.采用单行方式定义函数；注意：**当在命令行上定义函数时，必须记得在每个命令后加一个分号，分号表示本条命令的终止**。如下例所示：
    ```
    [njust@njust tutorials]$ function divem { echo $[$1 / $2 ]; }
    [njust@njust tutorials]$ divem 100 3
    33
    ```
    - b.采用多行方式定义函数；在定义时，bash shell会使用次提示符\>来提示输入更多命令。用这种方法，**不用在每天命令的末尾加一个分号，只要按下回车键即可**。如下所示：
    ```
    [njust@njust tutorials]$ function mulem {
    > echo $[ $1 * $2 ]
    > }
    [njust@njust tutorials]$ mulem 2 5
    10
    ```
##### 9.2 在\.bashrc文件中定义函数
- 在命令行中定义函数的缺点是：当退出shell时，函数就消失了。**一个非常简单的方法是将函数定义在一个特定的位置，这个位置在每次启动一个新shell时，都会由shell重新载入，这个特定的位置即\.bashrc文件**。bash shell在每次启动时都会在主目录下查找这个文件，不管是交互式的shell，还是从现有shell中启动新的shell。
- **9.2.1 直接定义函数**：可以在主目录下的\.bashrc文件中定义函数，许多Linux发行版中已经在\.bashrc中定义了一些东西，所以注意不要误删！！！。**将你写的函数添加至文件末尾即可**。如下例所示的函数addem函数会在启动新的bash shell时生效，之后就可以在系统上的任意位置使用这个函数了。
    ```
    # .bashrc

    # Source global definitions
    if [ -f /etc/bashrc ]; then
        . /etc/bashrc
    fi

    # Uncomment the following line if you don't like systemctl's auto-paging feature:
    # export SYSTEMD_PAGER=

    # User specific aliases and functions

    function addem {
        echo $[ $1 + $2 ]
    }
    ```
- **9.2.2 读取函数文件**：只要是在shell脚本中，都可以使用source命令(或者它的别名点操作符)将库文件中的函数添加到\.bashrc脚本中。如下例所示：
    ```
    # .bashrc

    # Source global definitions
    if [ -f /etc/bashrc ]; then
        . /etc/bashrc
    fi

    # Uncomment the following line if you don't like systemctl's auto-paging feature:
    # export SYSTEMD_PAGER=

    # User specific aliases and functions

    function addem {
        echo $[ $1 + $2 ]
    }

    . /home/njust/tutorials/myfuncs  # 将库文件myfuncs中的函数添加到.bashrc中
    ```
- **要确保库文件的路径正确，以便bash shell能找到该库文件**。下次启动shell时，库文件中的所有函数都可以在命令行界面下使用。如下所示：
    ```
    [njust@njust ~]$ source .bashrc
    [njust@njust ~]$ addem 5 10
    15
    ```
#### 10.资料下载
- [笔记，欢迎star,follow,fork......](https://github.com/cdlwhm1217096231/Linux/tree/master/Shell%E8%84%9A%E6%9C%AC%E7%BC%96%E7%A8%8B)
