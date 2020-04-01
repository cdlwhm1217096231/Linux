### 技术交流QQ群:1027579432，欢迎你的加入！
### 本教程使用Linux发行版Centos7.0系统，请您注意~

#### 1.for命令
- bash shell提供了for命令，允许你创建一个遍历一系列值的循环。每次迭代都使用其中的一个值来执行已定义好的一组命令，for命令的基本格式如下所示：
    ```
    for var in list
    do 
        命令...
    done
    ```
- 在list参数中，需要提供迭代需要的一系列值。可以通过几种不同的方法指定列表中的值。
- do和done语句中之间输入的命令可以是一条或者多条标准的bash shell命令。
- 也可以将do语句和for语句放在同一行，但是必须用分号将其同列表中的值分开：for var in list; do
##### 1.1 读取列表中的值
- for命令最基本的用法就是遍历for命令自身定义的一系列值，如下例所示：
    ```
    #!/bin/bash


    for li in A B C D E F G
    do
        echo The next state is $li
    done

    # 结果
    [njust@njust tutorials]$ ./dana.sh
    The next state is A
    The next state is B
    The next state is C
    The next state is D
    The next state is E
    The next state is F
    The next state is G
    ```
##### 1.2 读取列表中的复杂值
- 当shell看到list列表中的单引号并尝试使用它们来定义一个单独的数据值时，结果会一团糟。如下例所示：
    ```
    #!/bin/bash

    for var in I don't know if this'll work
    do 
        echo "word:$var"
    done

    # 结果
    word:I
    word:dont know if thisll
    word:work
    ```
- 两种解决方法：
    - 使用转义字符来将单引号进行转义；
    - 使用双引号来定义用到单引号的值；
    ```
    #!/bin/bash

    for var in I don\'t know if "this'll" work
    do 
        echo "word:$var"
    done

    # 结果
    [njust@njust tutorials]$ ./dana1.sh 
    word:I
    word:don't
    word:know
    word:if
    word:this'll
    word:work
    ```
- 注意：**for循环假定每个值都是用空格分割的，如果有包含空格的数据值，就会陷入麻烦**。如下例所示：
    ```
    #!/bin/bash


    for var in New York New Mexico North Carolina
    do
        echo "Now going to $var"
    done

    # 结果
    [njust@njust tutorials]$ ./dana2.sh 
    Now going to New
    Now going to York
    Now going to New
    Now going to Mexico
    Now going to North
    Now going to Carolina
    ```
- for命令用空格来划分列表中的每个值，如果在单独的数据值中有空格，就必须使用双引号将这些值圈起来，在某个值两边使用双引号时，shell并不会将双引号当成值的一部分。如下例所示：
    ```
    #!/bin/bash


    for var in "New York" "New Mexico" "North Carolina"
    do
        echo "Now going to $var"
    done

    # 结果
    [njust@njust tutorials]$ ./dana2.sh 
    Now going to New York
    Now going to New Mexico
    Now going to North Carolina
    ```
##### 1.3 从变量读取列表list
- 通常情况下，是将一系列值都集中存储在一个变量，然后需要遍历变量中的整个列表，如下例所示：
    ```
    #!/bin/bash

    list="Red Green Blue"

    list=$list" Blank"  # 使用赋值语句向$list变量包含的已有列表中添加一个值Blank，这是向变量中存储的已有文本字符串尾部添加文本的常用方法
    for var in $list
    do
        echo "The current color is $var"
    done

    # 结果
    [njust@njust tutorials]$ ./dana3.sh 
    The current color is Red
    The current color is Green
    The current color is Blue
    The current color is Blank
    ```
##### 1.4 从命令读取值
- 生成列表中所需值的另一途径就是使用命令的输出。可以使用**命令替换**来执行任何能产生输出的命令，然后在for命令中使用该命令的输出。如下例所示：
    ```
    #!/bin/bash

    file="color"


    for c in $(cat $file)
    do
        echo "beautiful color:$c"
    done


    # 结果
    [njust@njust tutorials]$ ./dana4.sh 
    beautiful color:Red
    beautiful color:Green
    beautiful color:Bule
    beautiful color:Pink
    ```
- 上述情况中每种颜色都在单独的一行，而不是通过空格分隔的。但是，**这并没有解决数据中用空格的情况**。
##### 1.5 更改字段分隔符
- 造成for命令中以空格为分隔符的原因是特殊的环境变量IFS(**内部字段分隔符**)，IFS环境变量定义了bash shell用作字符分隔符的一些列字符。默认情况下，bash shell会将下面的字符当成字段分隔符：
    - 空格
    - 制表符
    - 换行符
- 如果bash shell**在数据中**看到了这些字符中的任意一个，它就会假定这表明了列表中一个新数据字段的开始。这会导致在处理含有空格的数据时，显得十分麻烦。解决方法：**可以在shell脚本中临时更改IFS环境变量的值来限制被bash shell当作字段分隔符的字符**，如下例所示：
    ```
    #!/bin/bash

    file="color"

    IFS=$'\n'   # 告诉bash shell在数据值中忽略空格和制表符，仅识别换行符！

    for c in $(cat $file)
    do
        echo "beautiful color:$c"
    done

    # 结果
    [njust@njust tutorials]$ ./dana4.sh 
    beautiful color:Red
    beautiful color:Green
    beautiful color:Bule
    beautiful color:Pink
    beautiful color:Deep Purple
    ```
- 在处理代码量较大的脚本时，可能在一个地方需要修改IFS值。在脚本的其他地方继续沿用默认的IFS值。一个可行的方法是**在改变IFS之前保存原来的IFS值，之后再恢复它**，这样保证了在脚本的后续操作中继续使用的是默认的IFS值。
    ```
    IFS.OLD=$IFS

    IFS=$'\n'
    <在代码中使用新的IFS值>
    IFS=$IFS.OLD
    ```
- IFS环境变量的其他一些绝妙用法，假如你要遍历一个文件中用冒号分隔的值(比如/etc/passwd文件)，你需要做的就是将IFS设置为冒号IFS=：。
- 如果要指定多个IFS字符，只要将它们在赋值行串起来就行，如IFS=$'\n':;"。
##### 1.6 用通配符读取目录
- 可以用for命令来自动遍历目录中的文件，进行此操作时，**必须在文件名或路径名中使用通配符**。它会强制shell使用**文件扩展匹配**。文件扩展匹配是生成匹配指定通配符的文件名或路径名的过程。如下例所示：
    ```
    #!/bin/bash

    for file in /home/njust/tutorials/*
    do

        if [ -d "$file" ]  # 在linux中文件名或目录名中包含空格是合法的，要解决这个问题可以将$file变量用双引号圈起来。如果不这样的话遇到含空格的目录名或文件名时会报错！
        then
            echo "$file is a directory"
        elif [ -f "$file" ]
        then
            echo "$file is a file"
        fi
    done


    # 结果
    [njust@njust tutorials]$ ./dana5.sh 
    /home/njust/tutorials/case.sh is a file
    /home/njust/tutorials/color is a file
    /home/njust/tutorials/curry.sh is a file
    /home/njust/tutorials/dana is a file
    /home/njust/tutorials/dana1.sh is a file
    /home/njust/tutorials/dana2.sh is a file
    /home/njust/tutorials/dana3.sh is a file
    /home/njust/tutorials/dana4.sh is a file
    /home/njust/tutorials/dana5.sh is a file
    /home/njust/tutorials/dana.sh is a file
    ```
- 也可以在for命令中列出多个目录通配符，将目录查找和列表合并进同一个for语句，如下例所示：
    ```
    #!/bin/bash

    for file in /home/njust/.b* /home/njust/tutorials
    do

        if [ -d "$file" ]
        then
            echo "$file is a directory"
        elif [ -f "$file" ]
        then
            echo "$file is a file"
        else
            echo "$file doesn't exist"
        fi
    done

    # 结果
    [njust@njust tutorials]$ ./dana6.sh 
    /home/njust/.bash_history is a file
    /home/njust/.bash_logout is a file
    /home/njust/.bash_profile is a file
    /home/njust/.bashrc is a file
    /home/njust/tutorials is a directory
    ```
#### 2.C语言风格的for命令
- bash shell也支持一种for循环，它看起来与C语言风格的for循环类似，但有一些细微的不同。**bash中C语言风格的for循环基本格式如下**：
    ```
    for (( 变量初始化; 条件; 变量迭代变化 ))
    ```
- 注意：**有些部分并没有遵循bash shell标准的for命令**：
    - 变量赋值可以有空格；
    - 条件中的变量不以美元符开头；
    - 迭代过程的算式未使用expr命令格式；
- 以下是在bash shell程序中使用C语言风格的for命令：
    ```
    #!/bin/bash


    for (( i = 1; i <= 10; i++ )) 
    do 
        echo "The next number is $i"
    done

    # 结果
    [njust@njust tutorials]$ ./dana7.sh 
    The next number is 1
    The next number is 2
    The next number is 3
    The next number is 4
    The next number is 5
    The next number is 6
    The next number is 7
    The next number is 8
    The next number is 9
    The next number is 10
    ```
##### 2.1 使用多个变量
- C语言风格的for命令也允许为迭代使用多个变量。循环会单独处理每个变量，可以为每个变量定义不同的迭代过程，**尽管可以使用多个变量，但只能在for循环中定义一种条件**。
    ```
    #!/bin/bash


    # mutiple variables

    for (( a = 1, b = 10; a <= 10; a++, b-- ))
    do 
        echo "$a - $b"
    done

    # 结果
    [njust@njust tutorials]$ ./dana8.sh 
    1 - 10
    2 - 9
    3 - 8
    4 - 7
    5 - 6
    6 - 5
    7 - 4
    8 - 3
    9 - 2
    10 - 1
    ```
#### 3.while命令
- while命令允许定义一个要测试的命令，然后循环执行一组命令，只要定义的测试命令返回的是退出状态码0，它会在每次迭代的一开始测试test命令。在test命令返回非零退出状态码时，while命令会停止执行那组命令。
##### 3.1 while的基本格式
- while命令的格式如下：
    ```
    while test 命令
    do
        命令
    done
    ```
- while命令的关键点：**所指定的test 命令的退出状态码必须随着循环中运行的命令而改变。如果退出状态码不发生改变，while循环就会一直不停地执行下去**。最常见的test 命令的用法是用方括号检查循环命令中用到的shell变量的值，如下所示：
    ```
    #!/bin/bash

    var1=10
    while [ $var1 -gt 0 ]
    do 
        echo $var1
        var1=$[ $var1 - 1 ]
    done

    # 结果
    [njust@njust tutorials]$ ./dana9.sh 
    10
    9
    8
    7
    6
    5
    4
    3
    2
    1
    ```
##### 3.2 使用多个测试命令
- while命令允许你在while语句行定义多个测试命令。**只有最后一个测试命令的退出状态码会被用来决定什么时候结束循环**。如下例所示：
    ```
    #!/bin/bash

    var=10

    while echo $var
        [ $var  -ge 0 ]
    do
        echo "This is inside in the loop"
        var=$[ $var - 1 ]
    done

    # 结果
    [njust@njust tutorials]$ ./dana10.sh 
    10
    This is inside in the loop
    9
    This is inside in the loop
    8
    This is inside in the loop
    7
    This is inside in the loop
    6
    This is inside in the loop
    5
    This is inside in the loop
    4
    This is inside in the loop
    3
    This is inside in the loop
    2
    This is inside in the loop
    1
    This is inside in the loop
    0
    This is inside in the loop
    -1
    ```
- 在含有多个命令的while语句中，在每次迭代中所有测试命令都会被执行，包括测试命令失败的最后一次迭代。注意：**指定多个测试命令时，每个测试命令都出现在单独的一行**。
#### 4.until命令
- until命令与while命令工作方式相反，until命令要求你指定一个**通常返回非零状态码的测试命令**。只有测试命令的退出状态码非零，bash shell才会执行循环中列出的命令。**一旦测试命令返回退出状态码0，循环就结束了**。until命令的基本格式如下所示：
    ```
    until test 命令
    do
        命令
    done
    ```
- **可以在until命令语句中放入多个测试命令，只有最后一个命令的退出状态码决定了了bash shell是否执行已定义的命令**。如下例所示：
    ```
    #!/bin/bash


    var=100

    until [ $var -eq 0 ]
    do
        echo $var
        var=$[ $var - 25 ]
    done

    # 结果
    [njust@njust tutorials]$ ./dana11.sh 
    100
    75
    50
    25
    ```
- 与while命令相似，until命令在使用多个测试命令时要注意。shell会执行指定的多个测试命令，只有在最后一个命令成立时才会停止。如下例所示：
    ```
    #!/bin/bash


    var=100

    until echo $var
        [ $var -eq 0 ]
    do
        echo "Inside the loop: $var"
        var=$[ $var - 25 ]
    done

    # 结果
    [njust@njust tutorials]$ ./dana12.sh 
    100
    Inside the loop: 100
    75
    Inside the loop: 75
    50
    Inside the loop: 50
    25
    Inside the loop: 25
    0
    ```
#### 5.嵌套循环
- 循环语句可以在循环内使用任意类型的命令，包括其他循环命令。这种循环称为嵌套循环。注意：**使用嵌套循环时，在迭代中使用迭代，与命令运行的次数是乘积关系**。如下例所示：
    ```
    #!/bin/bash

    for (( a = 1; a <= 3; a++ ))
    do
        echo "Starting loop $a:"
        for (( b = 1; b <= 3; b++ ))
        do
            echo "  Inside loop:$b"
        done
    done

    # 结果
    [njust@njust tutorials]$ ./dana13.sh 
    Starting loop 1:
    Inside loop:1
    Inside loop:2
    Inside loop:3
    Starting loop 2:
    Inside loop:1
    Inside loop:2
    Inside loop:3
    Starting loop 3:
    Inside loop:1
    Inside loop:2
    Inside loop:3
    ```
- 在混用循环命令时也一样，比如在while循环内部放置一个for循环。如下例所示：
    ```
    #!/bin/bash


    var1=5

    while [ $var1 -ge 0 ]
    do
        echo "Outer loop: $var1"
        for (( var2 = 1; $var2 < 3; var2++ ))
        do
            var3=$[ $var1 * $var2 ]
            echo "  Inner loop: $var1 * $var2 = $var3"
        done
        var1=$[ $var1 - 1 ]
    done

    # 结果
    [njust@njust tutorials]$ ./dana14.sh 
    Outer loop: 5
    Inner loop: 5 * 1 = 5
    Inner loop: 5 * 2 = 10
    Outer loop: 4
    Inner loop: 4 * 1 = 4
    Inner loop: 4 * 2 = 8
    Outer loop: 3
    Inner loop: 3 * 1 = 3
    Inner loop: 3 * 2 = 6
    Outer loop: 2
    Inner loop: 2 * 1 = 2
    Inner loop: 2 * 2 = 4
    Outer loop: 1
    Inner loop: 1 * 1 = 1
    Inner loop: 1 * 2 = 2
    Outer loop: 0
    Inner loop: 0 * 1 = 0
    Inner loop: 0 * 2 = 0
    ```
#### 6.循环处理文件数据
- 通常必须遍历存储在文件中的数据，这要求结合已讲过的两种技术：
    - 使用嵌套循环；
    - 修改IFS环境变量；
- 通过修改IFS环境变量，就能强制for命令将文件中的每行都当成单独的一个条目来处理，即使数据中有空格也是如此。一旦从文件中提取出了单独的行，可能需要再次利用循环来提取行中的数据。典型的案例是**处理/etc/passwd文件中的数据，逐行遍历/etc/passwd文件，并将IFS变量的值改成冒号，这样就能分割开每行中的各个数据段**。如下例所示：
    ```
    #!/bin/bash

    IFS.OLD=$IFS

    IFS=$'\n'
    for var in $( cat /etc/passwd )
    do
        echo "Values in $var -"
        IFS=:
        for v in $var
        do
            echo "  $v"
        done
    done

    # 结果
    [njust@njust tutorials]$ ./dana15.sh 
    Values in root:x:0:0:root:/root:/bin/bash -
    root
    x
    0
    0
    root
    /root
    /bin/bash
    ```
#### 7.控制循环
##### 7.1 break命令
- break语句是退出循环的一个简单方法。可以使用break命令来退出任意类型的循环，包括while循环和until循环。
- **跳出单个循环**：在shell执行break命令时，它会尝试跳出当前正在执行的循环。
    ```
    #!/bin/bash

    for var in 1 2 3 4 5 6 7 8 9 10
    do
        if [ $var -eq 5 ]
        then
            break
        fi
        echo "Current number: $var"
    done
    echo "The for loop is completed"

    # 结果
    [njust@njust tutorials]$ ./dana16.sh 
    Current number: 1
    Current number: 2
    Current number: 3
    Current number: 4
    The for loop is completed
    ```
- 跳出单个循环的情景同样也适用于while和until循环，如下例所示：
    ```
    #!/bin/bash


    var=1

    while [ $var -lt 10 ]
    do
        if [ $var -eq 5 ]
        then
            break
        fi
        echo "Iteration: $var"
        var=$[ $var + 1 ]
    done

    echo "The while loop is completed"

    # 结果
    [njust@njust tutorials]$ ./dana17.sh 
    Iteration: 1
    Iteration: 2
    Iteration: 3
    Iteration: 4
    The while loop is completed
    ```
- **跳出内部循环**：在处理多个循环时，**break命令会自动终止你所在的最内层的循环**。
    ```
    #!/bin/bash


    for (( a =  1; a < 4; a++ ))
    do
        echo "Outer loop:$a"
        for (( b = 1; b < 100; b++ ))
        do
            if [ $b -eq 5 ]
            then
                break
            fi
            echo "  Inner loop:$b"
        done
    done

    # 结果
    [njust@njust tutorials]$ ./dana18.sh 
    Outer loop:1
        Inner loop:1
        Inner loop:2
        Inner loop:3
        Inner loop:4
    Outer loop:2
        Inner loop:1
        Inner loop:2
        Inner loop:3
        Inner loop:4
    Outer loop:3
        Inner loop:1
        Inner loop:2
        Inner loop:3
        Inner loop:4
    ```
- **跳出外部循环**：有时候你在内部循环，**但需要停止外部循环**。break命令接收单个命令行参数值：break n。n指定了要跳出的循环层级，默认情况下，n等于1，表示跳出的是当前的循环。如果n设置为2，break命令会停止下一级的外部循环。如下例所示：
    ```
    #!/bin/bash

    for (( a = 1; a < 4; a++ ))
    do
        echo "Outer loop:$a"
        for (( b = 1; b < 100; b++ ))
        do
            if [ $b -gt 4 ]
            then
                break 2
            fi
            echo "  Inner loop:$b"
        done
    done 

    # 结果
    [njust@njust tutorials]$ ./dana19.sh 
    Outer loop:1
        Inner loop:1
        Inner loop:2
        Inner loop:3
        Inner loop:4
    ```
##### 7.2 continue命令
- continue命令可以**提前中止某次循环中的命令**，但并不会完全终止整个循环。可以在循环内部设置shell不执行命令的条件。如下例所示：
    ```
    #!/bin/bash

    for (( a = 1; a < 15; a++ ))
    do
        if [ $a -gt 5 ] && [ $a -lt 10 ]
        then
            continue
        fi
        echo "Iteration number: $a"
    done

    # 结果
    Iteration number: 1
    Iteration number: 2
    Iteration number: 3
    Iteration number: 4
    Iteration number: 5
    Iteration number: 10
    Iteration number: 11
    Iteration number: 12
    Iteration number: 13
    Iteration number: 14
    ```
- 也可以在while或until循环中使用continue命令，但是特别小心。**当shell执行continue命令时，它会跳过剩余的命令。如果你在其中某个条件里对测试条件变量进行增值，问题就会出现**。
    ```
    #!/bin/bash

    var1=0

    while echo "while iteration: $var1"
          [ $var1 -lt 15 ]
    do
        if [ $var1 -gt 5 ] && [ $var1 -lt 10 ]
        then
            continue  # 当shell执行continue命令时，它跳过了while循环中余下的命令。
        fi
        echo "  Inside iteration number:$var1"
        var1=$[ $var1 + 1 ]
    done

    # 结果
    [njust@njust tutorials]$ ./dana21.sh 
    while iteration: 0
    Inside iteration number:0
    while iteration: 1
    Inside iteration number:1
    while iteration: 2
    Inside iteration number:2
    while iteration: 3
    Inside iteration number:3
    while iteration: 4
    Inside iteration number:4
    while iteration: 5
    Inside iteration number:5
    while iteration: 6
    while iteration: 6
    while iteration: 6
    while iteration: 6
    while iteration: 6
    while iteration: 6
    while iteration: 6
    while iteration: 6
    while iteration: 6
    while iteration: 6
    ```
- 和break命令类似，continue命令也允许通过命令行参数指定要继续执行哪一级循环：continue n。其中n定义了要继续的循环层级，如下例所示：
    ```
    #!/bin/bash


    for (( a = 1; a <= 5; a++ ))
    do
        echo "Iteration: $a"
        for (( b = 1; b < 3; b++ ))
        do 
            if [ $a -gt 2 ] && [ $a -lt 4 ]
            then
                continue 2 
            fi
            var=$[ $a * $b ]
            echo "  The result of $a * $b is $var"
        done
    done 

    # 结果
    [njust@njust tutorials]$ ./dana22.sh 
    Iteration: 1
    The result of 1 * 1 is 1
    The result of 1 * 2 is 2
    Iteration: 2
    The result of 2 * 1 is 2
    The result of 2 * 2 is 4
    Iteration: 3
    Iteration: 4
    The result of 4 * 1 is 4
    The result of 4 * 2 is 8
    Iteration: 5
    The result of 5 * 1 is 5
    The result of 5 * 2 is 10
    ```
#### 8.处理循环的输出
- 在shell脚本中，可以对循环的输出使用管道或进行重定向。**可以通过在done命令后添加一个处理命令来实现**。如下例所示：
    ```
    #!/bin/bash


    for file in /home/njust/*
    do
        if [ -d "$file" ]
        then
            echo "$file is a directory"
        else
            echo "$file is a file"
        fi
    done > output.txt


    # 结果
    [njust@njust tutorials]$ cat output.txt 
    /home/njust/sentinel is a file
    /home/njust/tutorials is a directory
    /home/njust/公共 is a directory
    /home/njust/模板 is a directory
    /home/njust/视频 is a directory
    /home/njust/图片 is a directory
    /home/njust/文档 is a directory
    /home/njust/下载 is a directory
    /home/njust/音乐 is a directory
    /home/njust/桌面 is a directory
    ```
- 上述方法同样适用于将循环的结果通过管道传给另一个命令，如下例所示：
    ```
    #!/bin/bash

    for state in "North Dakota" Pink Blue Green Red
    do
        echo "$state is the next place to go"
    done | sort

    echo "This completes our travels"


    # 结果
    [njust@njust tutorials]$ ./dana24.sh 
    Blue is the next place to go
    Green is the next place to go
    North Dakota is the next place to go
    Pink is the next place to go
    Red is the next place to go
    This completes our travels
    ```
#### 9.循环实例
- **查找可执行文件**
    ```
    #!/bin/bash


    IFS=:

    for folder in $PATH

    do
        echo "$folder"
        for file in $folder/*
        do
            if [ -x $file ]
            then
                echo "  $file"
            fi
        done
    done

    # 结果
    /sbin/vgextend
    /sbin/vgimport
    /sbin/vgimportclone
    /sbin/vgmerge
    /sbin/vgmknodes
    /sbin/vgreduce
    /sbin/vgremove
    /sbin/vgrename
    /sbin/vgs
    /sbin/vgscan
    /sbin/vgsplit
    /sbin/vigr
    ```
- **创建多个用户账户**
    ```
    #!/bin/bash


    input="users.txt"  # users.txt文件需提前创建，文件内容为：userid,username

    while IFS=',' read -r userid name
    do
        echo "adding $userid"
        useradd -c "$name" -m $userid

    done < "$input"

    # 结果，必须用root用户才能运行此脚本，因为useradd命令需要root权限！
    [root@njust tutorials]# ./dana26.sh
    adding C001
    adding D002
    adding H003
    adding J004

    # 验证
    [root@njust tutorials]# tail /etc/passwd
    postfix:x:89:89::/var/spool/postfix:/sbin/nologin
    ntp:x:38:38::/etc/ntp:/sbin/nologin
    tcpdump:x:72:72::/:/sbin/nologin
    njust:x:1000:1000:njust:/home/njust:/bin/bash
    shenchao:x:1001:1001::/home/shenchao:/bin/bash
    userid:x:1002:1003:name:/home/userid:/bin/bash
    C001:x:1003:1004:curry:/home/C001:/bin/bash
    D002:x:1004:1005:durant:/home/D002:/bin/bash
    H003:x:1005:1006:harden:/home/H003:/bin/bash
    J004:x:1006:1007:james:/home/J004:/bin/bash
    ```
#### 10.资料下载
- [笔记，欢迎star,follow,fork......](https://github.com/cdlwhm1217096231/Linux/tree/master/Shell%E8%84%9A%E6%9C%AC%E7%BC%96%E7%A8%8B)