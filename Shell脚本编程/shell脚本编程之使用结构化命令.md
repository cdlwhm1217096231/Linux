### 技术交流QQ群:1027579432，欢迎你的加入！
### 本教程使用Linux发行版Centos7.0系统，请您注意~
- **结构化命令**：许多程序要求对shell脚本中的命令添加一些逻辑流程控制，有一类命令会根据条件使脚本跳过某些命令。这样的一类命令就称为结构化命令。
#### 1.使用if-then语句
- 最简单的结构化语句是if-then语句，其语法格式如下：
    ```
    if 命令
    then
        命令
    fi
    ```
- if-then语句另一种的写法:
    ```
    if 命令; then
    命令
    fi
    ```
- 执行流程：
    - 如果if后面那个命令的退出状态码为0，则then之后命令会被执行；
    - 如果if后面那个命令的退出状态码是其他值，则then之后命令不会被执行，bash shell会继续执行脚本中的下一个命令；
    - fi语句用来表示if-then语句到此结束；
- 实例如下：
    ```
    #!/bin/bash


    # testing the if statement

    if pwd
    then
      echo "It Worked"
    fi

    # 结果
    [njust@njust tutorials]$ ./curry.sh 
    /home/njust/tutorials
    It Worked
    ```
- 反例如下：[注意]**bash shell会跳过then部分的echo语句，同时运行if语句中的那个错误命令所生成的错误消息依然会显示在脚本的输出中**。
    ```
    #!/bin/bash

    if notCommand
    then
       echo "It worked"
    fi

    echo "skipped 'then' statement"

    # 结果
    [njust@njust tutorials]$ ./durant.sh 
    ./durant.sh:行3: notCommand: 未找到命令
    skipped 'then' statement
    ```
- 在then部分，可以使用不止一条命令。bash shell会将这些命令当成一个语句块。实例如下：
    ```
    #!/bin/bash

    testuser=njust

    if grep $testuser /etc/passwd  # grep命令在/etc/passwd文件中查找某个用户当前是否在系统上使用
    then
    echo This is my first command
    echo This is my second command
    echo I can even put in other commands besides echo:
    ls -l /home/$testuser/.b*  # 显示一些文本信息并列出该用户HOME目录的bash文件
    fi

    # 结果
    This is my first command
    This is my second command
    I can even put in other commands besides echo:
    -rw-------. 1 njust njust 6013 3月  12 10:50 /home/njust/.bash_history
    -rw-r--r--. 1 njust njust   18 8月   8 2019 /home/njust/.bash_logout
    -rw-r--r--. 1 njust njust  193 8月   8 2019 /home/njust/.bash_profile
    -rw-r--r--. 1 njust njust  231 8月   8 2019 /home/njust/.bashrc
    ```
#### 2.if-then-else语句
- if-then语句中，不管命令是否成功执行，都只有一种选择。如果if后面的命令返回一个非零退出状态码，bash shell就会继续执行脚本中的下一条命令。if-then-else语句就是解决返回非零退出状态码的情况。语法格式：
    ```
    if 命令
    then 
        命令
    else
        命令
    fi
    ```
- 执行流程：
    - 当if语句中的命令返回状态码为0时，then部分的命令会被执行；
    - 当if语句中的命令返回状态码非0时，else部分的命令会被执行；
- 实例如下：
    ```
    #!/bin/bash

    testuser=njuster

    if grep $testuser /etc/passwd
    then
    echo This is my first command
    echo This is my second command
    echo I can even put in other commands besides echo:
    ls -l /home/$testuser/.b*
    else
    echo The user $testuser does not exist on this system.
    echo
    fi
    # 结果
    [njust@njust tutorials]$ ./harden.sh 
    The user njuster does not exist on this system.

    ```
#### 3.嵌套if-then
- 可以使用elif代替多个if-then语句。具体范例如下：
    ```
    #!/bin/bash

    testuser=njuster

    if grep $testuser /etc/passwd
    then
    echo The user $testuser exists on this system.
    elif ls -d /home/$testuser
    then
    echo The user $testuser does not exist on this system.
    echo However, $testuser has a directory.
    else
    echo The user $testuser does not exist on this system.
    echo And,$testuser does not have a directory.
    fi

    # 结果
    [njust@njust tutorials]$ ./harden.sh 
    ls: 无法访问/home/njuster: 没有那个文件或目录
    The user njuster does not exist on this system.
    And,njuster does not have a directory.
    ```
- 可以继续将多个elif命令串起来，形成一个大的if-then-elif嵌套组合。具体形式如下：
    ```
    if 命令
    then
    命令
    elif 命令
    then
    命令
    elif 命令
    then 
    命令
    ......
    fi
    ```
- **尽管elif语句的代码看起来很清晰，但脚本的逻辑不好。后面会使用case命令代替if-then语句的大量嵌套**。
#### 4.test命令
- 目前为止，if语句中看到的都是普通的shell命令。由于if-then语句不能测试命令退出状态码之外的条件。因此，bash shell中有个工具可以帮你通过if-then语句测试其他条件。
- test命令提供了在if-then语句中测试不同条件的路径。**如果test命令中列出的条件成立，test命令就会退出并返回退出状态码；如果条件不成立，test命令就会退出并返回非零的退出状态码**。于是，if-then语句就不会被执行。语法格式如下：
    ```
    test 测试条件
    ```
- 测试条件是test命令要测试的一系列参数和值。当用在if-then语句中是，形式如下所示：
    ```
    if test 测试条件
    then
    命令
    fi
    ```
- 如果不写test语句中的测试条件，它会以非零的退出状态码退出，并执行else语句块，实例如下：
    ```
    #!/bin/bash

    # Testing the test command

    if test
    then
    echo No expression returns a ture
    else
    echo No expression returns a false
    fi

    # 结果
    [njust@njust tutorials]$ ./james.sh 
    No expression returns a false
    ```
- 当加入一个条件时，test命令会测试该条件。例如，可以使用test命令确定变量中是否有内容，如下所示：
    ```
    #!/bin/bash

    my_variable="Curry"

    if test $my_variable
    then
    echo The $my_variable expression returns a true.
    else 
    echo The $my_variable expression returns a false.
    fi

    # 结果
    [njust@njust tutorials]$ ./tlu.sh 
    The Curry expression returns a true.
    ```
- bash shell 提供了另一种条件测试方法。无需在if-then语句中声明test命令。语法格式如下：
    ```
    if [ 测试条件 ]
    then
    命令
    fi
    ```
- 方括号定义了测试条件。注意：**测试条件的左右两侧都要留一个空格，否则会出错**。test命令可以判断三类条件：
    - 数值比较
    - 字符串比较
    - 文件比较

##### 4.1 数值比较
- test命令最常见于对两个数值进行比较。测试两个值时可以使用的条件参数如下所示：
    ```
    比较                            描述
    num1 -eq num2                   等于
    num1 -ge num2                   大于或等于
    num1 -gt num2                   大于
    num1 -le num2                   小于或等于
    num1 -lt num2                   小于
    num1 -ne num2                   不等于
    ```
- 数值比较测试案例：
    ```
    #!/bin/bash

    # using numeric test evaluations

    value1=10
    value2=20

    if [ $value1 -gt 5 ]
    then
    echo The test value $value1 is greater than 5.
    fi


    if [ $value1 -eq $value2 ]
    then
    echo The values are equal.
    else
    echo The values are different.
    fi


    # 结果
    [njust@njust tutorials]$ ./num.sh 
    The test value 10 is greater than 5.
    The values are different.
    ```
- 但是，涉及浮动值时，数值条件测试会有一个限制。原因：**bash shell只能处理整数，如果你只是要通过echo语句来显示这个结果，那没问题**。实例如下：
    ```
    #!/bin/bash

    value=3.1415926

    echo The test value is $value.

    if [ $value1 -gt 5 ]
    then
    echo The test value $value is greater than 5.
    fi

    # 结果
    [njust@njust tutorials]$ ./num2.sh 
    The test value is 3.1415926.
    ./num2.sh: 第 7 行:[: -gt: 期待一元表达式
    ```
##### 4.2 字符串比较
- 条件测试还允许比较字符串的值。测试两个字符串之间的比较语法如下：
    ```
    比较                             描述
    str1 = str2                    检查两者是否相同
    str1 != str2                   检查两者是否不同
    str1 < str2                    检查str1是否小于str2
    str1 > str2                    检查str1是否大于str2
    -n str1                        检查str1的长度是否非0
    -z str1                        检查str1的长度是否为0
    ```
- **比较字符串的相等性时，比较测试会将所有的标点和大小写情况都考虑在内**。字符串的相等性案例如下：
    ```
    #!/bin/bash

    testuser=njust

    if [ $USER = $testuser ]
    then
    echo welcome $testuser
    fi

    # 结果
    [njust@njust tutorials]$ ./str1.sh 
    welcome njust
    ```
- 字符串的不等条件判断案例如下：
    ```
    #!/bin/bash

    testuser=baduser

    if [ $USER != $testuser ]
    then
    echo This is not $testuser
    else
    echo welcome $testuser
    fi

    # 结果
    [njust@njust tutorials]$ ./str1.sh 
    This is not baduser
    ```
- **字符串顺序**：要测试一个字符串是否比另一个字符串大或小时，经常要考虑下面两个问题：
    - 大于号或小于号必须转义，否则shell会把它们当作重定向符号，把字符串值当作文件名；
    - 大于和小于顺序和sort命令所采用的不同；
- 字符串比较是常出现的错误案例及解决方法：
    ```
    #!/bin/bash

    value1=baseball
    value2=football

    if [ $value1 > $value2 ]
    then
    echo $value1 is greater than $value2.
    else
    echo $value1 is less than $value2.
    fi

    # 结果
    [njust@njust tutorials]$ ./str2.sh 
    baseball is greater than football.

    # 解决方法：对大于号进行转义
    #!/bin/bash

    value1=baseball
    value2=football

    if [ $value1 \>  $value2 ]
    then
    echo $value1 is greater than $value2.
    else
    echo $value1 is less than $value2.
    fi

    # 结果
    [njust@njust tutorials]$ ./str2.sh 
    baseball is less than football.
    ```
- 原因分析：脚本中的大于号被解释成了输出重定向，因此创建了一个名为football的文件。由于重定向的顺利执行，test命令返回了退出状态码0，if语句误以为所有命令都已经执行完成了。
    ```
    [njust@njust tutorials]$ ls -l football 
    -rw-rw-r--. 1 njust njust 0 3月  17 14:33 football
    ```
- 第二个问题一般出现在经常处理大小写的场合。sort命令处理大写字母的方法刚好和test命令相反。具体原因：**比较测试中使用的是标准的ASCII顺序，根据每个字符的ASCII数值来决定排序结果。sort命令使用的是系统的本地化语言设置中定义的排序顺序**。具体案例如下：
    ```
    #!/bin/bash

    value1=Curry
    value2=curry

    if [ $value1 \>  $value2 ]
    then
    echo $value1 is greater than $value2.
    else
    echo $value1 is less than $value2.
    fi

    # 结果
    [njust@njust tutorials]$ ./str2.sh 
    Curry is less than curry.


    ########  sort命令  #########
    [njust@njust tutorials]$ cat testfile 
    Curry
    curry

    # 结果
    [njust@njust tutorials]$ sort testfile 
    curry
    Curry
    ```
- **字符串大小**：-n和-z可以检查一个变量中是否为空。具体案例如下：
    ```
    #!/bin/bash

    val1=testing
    val2=""


    if [ -n $val1 ]
    then
    echo "The string '$val1' is not empty."
    else
    echo "The string '$val1' is empty."
    fi


    if [ -z $val2 ]
    then
    echo "The string '$val2' is empty."
    else 
    echo "The string '$val2' is not empty."
    fi


    if [ -z $val3 ]
    then 
    echo "The string '$val3' is empty."
    else
    echo "The string '$val3' is not empty."
    fi

    # 结果
    [njust@njust tutorials]$ ./str3.sh 
    The string 'testing' is not empty.
    The string '' is empty.
    The string '' is empty.
    ```
##### 4.3 文件比较
- 文件比较可能是shell编程中最强大、用的最多的比较形式。它允许你测试Linux文件系统上文件和目录的状态。文件比较功能语法如下：
    ```
    比较                               描述
    -d  file                       检查file是否存在并是一个目录
    -e  file                       检查file是否存在
    -f  file                       检查file是否存在并是一个文件
    -r  file                       检查file是否存在并可读
    -s  file                       检查file是否存在并非空
    -w  file                       检查file是否存在并可写
    -x  file                       检查file是否存在并可执行
    -O  file                       检查file是否存在并属于当前用户所有
    -G  file                       检查file是否存在并且默认组与当前组相同
    file1  -nt file2               检查file1是否比file2新
    file1  -ot file2               检查file1是否比file2旧
    ```
- **检查目录**：-d测试会检查指定的目录是否存在于系统中。一般用于将文件写入目录或准备切换到某个目录中。
    ```
    #!/bin/bash

    dst_dir=/home/curry

    if [ -d $dst_dir ]
    then
    echo "The $dst_dir directory exists"
    cd $dst_dir
    ls
    else
    echo "The $dst_dir directory does not exists"
    fi


    # 结果
    [njust@njust tutorials]$ ./file1.sh 
    The /home/curry directory does not exists
    ```
- **检查对象是否存在**：-e允许你的脚本代码在使用文件或目录前先检查它们是否存在。
    ```
    #!/bin/bash

    location=$HOME

    file_name="sentinel"
    mid_path="tutorials"

    if [ -e $location ]
    then
    echo "OK on the $location directory."
    echo "Now checking on the file, $file_name."
    if [ -e $location/$mid_path/$file_name ]
    then
        echo "OK on the filename"
        echo "Updating Current Date..."
        date >> $location/$mid_path/$file_name
    else
        echo "File does not exist"
        echo "Nothing to update"
    fi

    else 
    echo "The $location directory does not exist."
    echo "Nothing to update"
    fi

    # 结果
    [njust@njust tutorials]$ ./file2.sh 
    OK on the /home/njust directory.
    Now checking on the file, sentinel.
    File does not exist
    Nothing to update

    [njust@njust tutorials]$ ./file2.sh 
    OK on the /home/njust directory.
    Now checking on the file, sentinel.
    OK on the filename
    Updating Current Date...
    ```
