### 技术交流 QQ 群:1027579432，欢迎你的加入！

### 本教程使用 Linux 发行版 Centos7.0 系统，请您注意~

#### 1.使用多个命令

- shell 脚本的关键之处在于输入多个命令并处理每个命令的结果，甚至需要将一个命令的结果传给另一个命令。shell 可以让多个命令串起来，一次执行。**如果要两个命令一起运行，可以将它们放在同一行，之间用逗号隔开**。
  ```
  [njust@njust tutorials]$ date;who
  2020年 03月 11日 星期三 22:39:16 CST
  njust    :0           2020-03-11 22:28 (:0)
  njust    pts/0        2020-03-11 22:35 (192.168.0.107)
  ```
- 上述方法的缺点：使用上述方法可以将任意多个命令串联在一起使用，最大命令行字符数不超过 255 个。对小型的脚本适用，当有很多脚本时，直接在命令行中输入整个命令就很麻烦。

#### 2.创建 shell 脚本文件

- 创建 shell 脚本文件时，**必须在文件的第一行指定使用的 shell 是哪种类型**，格式为：
  ```
  #!/bin/bash
  ```
- shell 脚本中注释一般以\#开头，shell 脚本不会处理注释的行。但是，shell 脚本的第一行是例外。**\#后的!会告诉 shell 用哪个 shell 来运行脚本**，shell 会根据命令在文件中出现的先后顺序进行处理。下面是创建脚本名为 demo 的文件。
  ```
  #!/bin/bash
  date
  who
  ```
- 存在的问题：如何让 bash shell 找到你创建的脚本文件？shell 会通过 PATH 环境变量来查找命令。PATH 环境变量被设置成只在一组目录中查找命令。
  ```
  [njust@njust tutorials]$ echo $PATH
  /usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/njust/.local/bin:/home/njust/bin
  ```
- 解决方法：让 shell 找到脚本文件，有两个方法：
  - 将 shell 脚本文件所处的目录添加到 PATH 环境变量中；
  - 在命令行中使用绝对或相对的路径来引用 shell 脚本文件（常用）；
- **由于你还没有执行文件的权限，这是由于 umask 变量被设置为 022，因此系统创建的文件只有读写权限**。使用下面的命令赋予文件有可执行权限。
  ```
  chmod u+x demo
  ```
- 为了引用当前目录下的文件，可以在 shell 中使用单点操作符.。正式执行脚本 demo
  ```
  ./demo
  ```

#### 3.显示消息

- 在 echo 命令后加上一个字符串，该命令就会显示出这个文本字符串。默认情况下，不需要使用引号将需要显示的字符串包含起来。
  ```
  [njust@njust tutorials]$ echo hello world
  hello world
  ```
- 当字符串中含有单引号或双引号时，可以使用双引号或单引号(注意叙述的顺序)包含该字符串。
  ```
  [njust@njust tutorials]$ echo "Let's see if this'll work"
  Let's see if this'll work
  ```
- **echo 命令可以添加到 shell 脚本中任何需要显示额外信息的地方**！
- 当需要把字符串和命令输出显示在同一行时，可以使用带参数 n 的 echo 命令，如下所示：

  ```
  #!/bin/bash

  echo -n "The time and date are: "
  date

  # 结果
  The time and date are: 2020年 03月 11日 星期三 23:07:03 CST
  ```

#### 4.使用变量

- 变量可以将临时信息存储在 shell 脚本中，便于和 shell 中其他的命令一起使用。
- **环境变量**：shell 维护一组环境变量，用来记录特定的系统信息。**可以使用 set 命令来显示一份完整的当前环境变量列表**。
- **在脚本中，可以在环境变量名称前加上美元符号\$从而来使用这些变量**。

  ```
  #!/bin/bash

  # print information about logger

  echo "User info for userid: $USER"
  echo UID: $UID
  echo HOME: $HOME

  # 结果
  User info for userid: njust
  UID: 1000
  HOME: /home/njust
  ```

- 注意：echo 命令中的环境变量会在脚本运行时替换成当前值，只要脚本在引号中出现美元符，它就会以为你在引用一个变量，因此在表示真实美元的含义时，需要在\$符号前加\转义字符。

  ```
  #!/bin/bash

  echo "The cost of the item is \$5."
  ```

- 此外，还可以通过\${变量名}的形式引用变量，变量名两侧的{}通常用于帮助识别美元符后的变量名。
- **用户变量**：除了环境变量外，shell 还允许在脚本中定义和使用自己的变量。变量名使用字母、数字或下划线组成的字符串表示，长度最长不超过 20 个。
- 注意：**使用等号将值赋给用户自定义的变量，在变量、等号和值之间不能出现空格**！！！
  ```
  var1=23  # 等号左右不能出现空格！！
  var2=demo
  var3=testing
  var4="hello shell"
  ```
- shell 脚本会自动决定变量值的数据类型。在脚本的整个生命周期内，shell 脚本定义的变量会一直存在，在 shell 脚本结束时会被删除。**用户变量也可以通过\$引用**。

  ```
  #!/bin/bash

  days=10
  guest="curry"
  echo "$guest checked in $days days ago"

  days=5
  guest="durant"
  echo "$guest cheked in $days days ago"

  # 结果
  curry checked in 10 days ago
  durant cheked in 5 days ago
  ```

- 变量每次引用时，都会输出当前赋给它的值。引用一个变量时需要使用美元符号\$，引用一个变量 var1 给另一个变量 var2 进行赋值时，被赋值的变量不要使用\$。对 var1 忘记使用\$号，就会使 var2 的赋值行变成普通的字符串。

  ```
  #!/bin/bash

  var1=10
  var2=$var1  # 在赋值语句中使用var1变量的值时，必须使用$符号
  echo The resulting value is $var2

  # 结果
  The resulting value is 10

  var2=var1;  # 错误的代表案例，输出结果是普通字符串
  echo The resulting value is $var2

  # 错误结果
  The resulting value is var1
  ```

- **命令替换**：shell 最有用的特性之一是从命令输出中提取出信息，并将其赋值给变量。将命令的输出赋值给变量后，就可以在脚本中使用了。有两种方法可以将命令输出赋值给变量：
  - 反引号字符`
  - \$()格式
- 命令替换允许你将 shell 命令的输出赋值给变量，具体如下所示：

  ```
  #!/bin/bash

  testing=`date`
  test=$(date)
  echo The date and time are: $testing
  echo The date and time are: $test

  # 结果
  The date and time are: 2020年 03月 12日 星期四 09:11:38 CST
  The date and time are: 2020年 03月 12日 星期四 09:11:38 CST
  ```

- 实例：通过命令替换获得当前日期并用它来生成唯一的文件名。

  ```
  #!/bin/bash

  # copy the /usr/bin/directory listing to a log file

  today=$(date +%y%m%d)  # +%y%m%d格式是告诉date命令将日期显示为两位数的年月日数字组合
  ls /usr/bin -al > log.$today

  # 结果
  生成log.200312日志文件
  ```

- 命令替换会创建一个子 shell 来运行对应的命令，由该子 shell 所执行命令是无法使用脚本中所创建的变量。在命令行中使用路径./运行命令时，也会创建子 shell；在运行命令时不加入路径，就不会创建子 shell。

#### 5.重定向输入和输出

- 重定向目的：想要保存某个命令的输出而不仅仅是让结果输出在屏幕上。
- **输出重定向**：最基本的重定向是将命令的输出发送在一个文件中。格式如下：
  ```
  具体命令 > 输出文件名
  ```
- 实例如下：
  ```
  [njust@njust tutorials]$ date > test6
  [njust@njust tutorials]$ cat test6
  2020年 03月 12日 星期四 09:40:09 CST
  ```
- **如果输出文件已经存在，重定向操作符会用新的文件数据覆盖已有文件**。
  ```
  [njust@njust tutorials]$ who > test6
  [njust@njust tutorials]$ cat test6
  njust    :0           2020-03-11 22:28 (:0)
  njust    pts/0        2020-03-12 08:39 (192.168.0.107)
  njust    pts/1        2020-03-12 08:41 (:0)
  ```
- 有时候，你并不想覆盖原始文件中的内容，而是想将命令的输出追加到已有文件中。**这种情况下，可以用>>来追加数据**。
  ```
  [njust@njust tutorials]$ date >> test6
  [njust@njust tutorials]$ cat test6
  njust    :0           2020-03-11 22:28 (:0)
  njust    pts/0        2020-03-12 08:39 (192.168.0.107)
  njust    pts/1        2020-03-12 08:41 (:0)
  2020年 03月 12日 星期四 09:43:41 CST
  ```
- **输入重定向**：输入重定向将文件的内容重定向到命令，而不是将命令输出重定向到文件。输入重定向的格式：
  ```
  具体命令 < 输入文件
  ```
- 具体实例：wc 命令可以统计文件中的数据，默认情况下会输出 3 个值。
  ```
  [njust@njust tutorials]$ wc < test6
  4  21 186  # 从左到右分别表示文本的行数、文本的词数、文本的字节数
  ```
- **内联输入重定向**：<<无需使用文件进行重定向，只需要在命令行中指定输入重定向的数据即可。注意：**必须指定一个文本标记来划分输入数据的起始和结尾。任何字符串都可以作为文本标记，但数据的起始和结尾文本标记必须一致**。格式：
  ```
  具体命令 >> EOF
  data
  EOF
  ```
- 在命令行中使用内联输入重定向时，shell 会用 PS2 变量中定义的次提示符来提示用户输入数据。
  ```
  [njust@njust tutorials]$ wc << EOF
  > test string 1  # >表示的就是次提示符
  > test string 2
  > test string 3
  > EOF
  3  9 42
  ```

#### 6.管道

- 管道的目的：一个命令的输出作为另一个命令的输入。
- 管道被放在命令之间，将一个命令的输出重定向到另一个命令中。基本格式如下：
  ```
  命令1 | 命令2
  ```
- 管道串器的两个命令不是依次执行的，Linux 系统实际上会同时运行这两个命令，在系统内部将它们连接起来。在第一个命令产生输出的同时，输出会被立即送个第二个命令。数据传输不会用的任何中间文件或缓冲区。
- 可以在一条命令中使用任意多条管道。可以持续地将命令的输出通过管道传给其他命令来细化操作。如下例所示：

  ```
  rpm -qa | sort | more  # 先生成已安装包的列表，再排序，最后再用more显示

  # 如果想更精致点，可以搭配使用重定向和管道将输出保存到文件中
  rpm -qa | sort > rpm.list
  ```

- **管道最流行的用法之一就是将产生的大量输出通过管道传给 more 命令，一般与 ls 命令结合使用**。

#### 7.执行数学运算

- 在 shell 中有两个途径进行数学运算。
  - expr 命令
  - 使用方括号
- **expr 命令允许在命令行中处理数学表达式，但特别笨拙**。许多 expr 命令操作符在 shell 中另有含义，当它们出现在 expr 命令中时，会得到一些诡异的结果。对那些容易被 shell 错误解释的字符，需要使用转义字符。
  ```
  [njust@njust tutorials]$ expr 5 * 2
  expr: 语法错误
  [njust@njust tutorials]$ expr 5 \* 2
  10
  ```
- 在 shell 脚本中 expr 命令也同样复杂，如下所示：

  ```
  #!/bin/bash

  var1=10
  var2=20
  var3=$(expr $var2 / $var1)  # 要将一个数学表达式的结果赋值给一个变量var3,也需要借助命令替换。
  echo The result is $var3

  # 结果
  The result is 2
  ```

- **在 bash 中，在将一个数学运算结果赋给一个变量时，可以使用美元符和[]将整个表达式圈起来**。
  ```
  [njust@njust tutorials]$ var1=$[1+34]
  [njust@njust tutorials]$ echo $var1
  35
  ```
- 用方括号执行 shell 运算比用 expr 命令方便很多，在 shell 脚本中也能看出。

  ```
  #!/bin/bash

  var1=10
  var2=20
  ```


    var3=`expr $var2 / $var1`
    echo the result is $var3

    var1=100
    var2=200
    var3=45


    var4=$[$var1 * ($var3 - $var2)]
    echo final result is $var4
    # 结果
    the result is 2
    final result is -15500
    ```

- **bash shell 数学运算符只支持整数运算**，这是一个巨大的限制。

  ```
  #!/bin/bash

  var1=100
  var2=45
  var3=$[$var1 / $var2]
  echo The final result is $var3

  # 结果
  The final result is 2
  ```

- 浮点数运算的解决方案：使用内建的 bash 计算器 bc。bash 计算器允许在命令行中输入浮点表达式，然后解释并计算该表达式，返回结果。
- 浮动运算是由内建变量 scale 控制的，这个值设置为你需要保留的小数点后几位。
  ```
  [njust@njust ~]$ bc -q  # -q参数表示不显示欢迎信息
  3.44 /5
  0
  scale=4
  3.44 / 5
  .6880
  quit
  ```
- bash 计算器还支持变量，如下所示：
  ```
  bc -q
  var1=10
  var1*4
  40
  var2=var1 / 5
  print var2
  2
  quit
  ```
- **在脚本中使用 bc**：可以使用命令替换运行 bc 命令，并将输出赋值给一个变量，基本格式如下：
  ```
  变量名=$(echo "可选项; 表达式" | bc)
  ```
- 实例：在脚本中使用 bc，如下所示。

  ```
  #!/bin/bash

  var1=$(echo "scale=4; 3.44/5" | bc)
  echo $var1
  .6880
  ```

- 上述方法适用于较短的运算，但有时候涉及更多的数字。需要进行大量的运算，在一个命令行中列出多个表达式就会麻烦。解决方法：使用内联输入重定向，它允许你直接在命令行中重定向数据。基本格式为：
  ```
  变量名=$(bc << EOF
  可选项
  语句
  表达式
  EOF)
  ```
- 在 bash 计算器中创建的变量只在 bash 计算器中有效，不能在 shell 脚本中使用。
  ```
  #!/bin/bash
  ```


    var1=10.46
    var2=43.67
    var3=33.2
    var4=71

    var5=$(bc << EOF
    scale=4
    a1 = ($var1 * $var2)
    b1 = ($var3 * $var4)
    a1 + b1
    EOF
    )
    echo final result is $var5
    # 结果
    final result is 2813.9882
    ```

#### 8.退出脚本

- shell 中运行的每个命令都使用退出状态码，shell 告诉它已经运行完毕。退出状态码是一个 0 到 255 的整数值，在命令结束时由命令传给 shell，可以捕捉这个值在脚本中使用。**Linux 专门提供了变量\$?来保存上一个已执行命令的退出状态码**。对于需要进行检查的命令，必须在其运行完毕后立即查看或使用\$?变量。
  ```
  [njust@njust tutorials]$ date
  2020年 03月 12日 星期四 10:39:19 CST
  [njust@njust tutorials]$ echo $?
  0
  ```
- Linux 退出状态码如下：
  ```
  0           命令成功结束
  1           一般性未知错误
  2           不适合的shell命令
  126         命令不可执行
  127         没找到命令
  128         无效的退出参数
  130         通过CTRL+C终止的命令
  ```
- exit 命令：默认情况下，shell 脚本会以脚本中最后一个命令的退出状态码退出。**用户可以改变这种默认行为，返回自己的状态码。eixt 命令允许在脚本结束时指定一个退出状态码**。

  ```
  #!/bin/bash

  var1=10
  var2=30
  var3=$[$var1 + $var2]
  echo The answer is $var3
  exit 5

  # 结果
  The answer is 40
  echo $?
  5

  # 也可以在exit命令的参数中使用变量
  exit $var3

  echo $?
  40

  # 注意：退出状态码最大为255，超过最大的255后，会通过取模运算得到最后的结果即实际值 % 256 = 最结果
  var1=10
  var2=30
  var3=$[$var1 * $var2]
  echo The answer is $var3
  exit $var3

  echo $?
  44
  ```

#### 9.资料下载

- [笔记，欢迎 star,follow,fork......](https://github.com/cdlwhm1217096231/Linux/tree/master/Shell%E8%84%9A%E6%9C%AC%E7%BC%96%E7%A8%8B)
