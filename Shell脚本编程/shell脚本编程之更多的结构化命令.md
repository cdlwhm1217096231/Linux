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
##### 6.用通配符读取目录
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
