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
