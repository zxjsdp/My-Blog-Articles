[转载]-Shell常用招式大全之入门篇
==============================

转载自segmentfault：<http://segmentfault.com/a/1190000002924882>

![Shell常用招式大全之入门篇](data/linux-shell-script-guide.png)

本教程分为入门篇，命令篇和实战篇，结合平时工作中使用Shell的经验编写。以实例为主，侧重于应用，总结了一些实用的技巧。

以下为本教程的《入门篇》，适于初学者快速入门以及老手查缺补漏。

- 第一招 HelloWorld
    - 第一式：echo
- 第二招 判断
    - 第一式：if
    - 判断原理
    - 第二式：test、[ ] 和 [[ ]]
    - 文件测试
    - 字符串比较
    - 整数比较
    - 第三式：&&、||
- 第三招：循环
    - 第一式：for
    - 第二式：while、until
- 第四招：变量
    - 第一式：整数
    - 整数的运算
    - 第二式：字符串
    - 替换
    - 截取子串
    - 通配删除
    - 第三式：数组
    - 普通数组
    - 关联数组
    - 第四式：将命令执行结果存入变量
    - \` \`与$( )
    - 换行符处理
- 第五招：重定向
    - 标准输入流、标准输出流、标准错误流
    - 重定向方式一览表
    - 第一式：重定向标准输出流(stdout)
    - 第二式：重定向标准错误流(stderr)
    - 第三式：重定向标准输入流(stdin)
- 第六招：管道
    - 第一式：管道的基本功能
    - 第二式：管道与while、read组合
    - 第三式：管道与xargs组合
- 第七招：通配
    - shell通配的原理
    - 通配符一览表
    - 第一式：*
    - 第二式：[ ]
    - 第三式：?
    - 第四式：{ }

# 第一招 HelloWorld
## 第一式：echo

    echo "Hello World"

    echo -n "Hello World"    # 不带换行

    echo -e '\e[0;33;1mHello\e[0m World'   # 带颜色的玩法
    echo -e '\e[0;33;4mHello\e[0m World'   # 带颜色+下划线
    echo -e '\e[0;33;5mHello\e[0m World'   # 带颜色+闪烁

格式为 `\e[背景色;前景色;高亮格式m`，请阅读[详细文档](http://blog.chinaunix.net/uid-15007890-id-3152717.html)后使用正确的姿势。

#第二招 判断
##第一式：if

    if true
    then
        echo "Hello World"
    else
        echo "Bug"
    fi

    if false
    then
        echo "Hello World"
    elif true
    then
        echo "Bug"
    else
        echo "Bee"
    fi

判断原理

`if`、`elif`会执行它后面跟着的命令，然后看返回值是否为0，如果为0则执行`then`下面的语句块，否则执行`else`下面的语句块。

    [casheywen@ubuntu:~]$ true
    [casheywen@ubuntu:~]$ echo $?
    0
    [casheywen@ubuntu:~]$ false
    [casheywen@ubuntu:~]$ echo $?
    1

注：

`true`、`false`事实上也为一个命令，`true`的返回码必为0，`false`的返回码必为1
`$?`为shell内置变量，用于存放上一个命令的返回码

##第二式：`test`、`[ ]` 和 `[[ ]]`

`test`、`[ ]`、`[[ ]]`实际上都是shell中的命令，执行之后会返回1或0，而这几个命令与`if`相结合可以达到我们所需要的许多判断功能，例如测试字符串是否为空的三种写法：

    s=""
    if [ -z ${s} ]
    then
        echo "empty"
    fi

    if [[ -z ${s} ]]
    then
        echo "empty"
    fi

    if test -z ${s}
    then
        echo "empty"
    fi

事实上，`if`后的`[ ]`、`[[ ]]`、`test`命令都是可以单独执行的，而根据`if`的判断原理，后续执行哪个分支也是由`[ ]`、`[[ ]]`、`test`的返回值来决定的，以下是单独执行它们的效果：

    [casheywen@ubuntu:~]$ s=""
    [casheywen@ubuntu:~]$ [ -z "${s}" ]
    [casheywen@ubuntu:~]$ echo $?
    0
    [casheywen@ubuntu:~]$ s="abc"
    [casheywen@ubuntu:~]$ test -z "${s}"
    [casheywen@ubuntu:~]$ echo $?
    1
    [casheywen@ubuntu:~]$ s="123"
    [casheywen@ubuntu:~]$ [[ 100 -lt ${s} ]]
    [casheywen@ubuntu:~]$ echo $?
    0

在性能方面`[ ]`和`test`性能基本相同，`[[ ]]`性能是最高的，为前两者的5倍左右（以`-d`运算符测试），所以建议尽量使用`[[ ]]`提高脚本性能。

文件测试

    运算符           描述                                      示例
    -e filename     如果 filename 存在，则为真                  [ -e /var/log/syslog ]
    -d filename     如果 filename 为目录，则为真         [ -d /tmp/mydir ]
    -f filename     如果 filename 为常规文件，则为真   [ -f /usr/bin/grep ]
    -L filename     如果 filename 为符号链接，则为真   [ -L /usr/bin/grep ]
    -r filename     如果 filename 可读，则为真         [ -r /var/log/syslog ]
    -w filename     如果 filename 可写，则为真        [ -w /var/mytmp.txt ]
    -x filename     如果 filename 可执行，则为真      [ -x /usr/bin/grep ]
    filename1 -nt filename2     如果 filename1 比 filename2 新，则为真  [ /tmp/install/etc/services -nt /etc/services ]
    filename1 -ot filename2     如果 filename1 比 filename2 旧，则为真  [ /boot/bzImage -ot arch/i386/boot/bzImage ]

字符串比较

    运算符     描述  示例
    -z string   如果 string 长度为零，则为真  [ -z "${myvar}" ]
    -n string   如果 string 长度非零，则为真  [ -n "${myvar}" ]
    string1 = string2   如果 string1 与 string2 相同，则为真     [ "${myvar}" = "abc" ]
    string1 != string2  如果 string1 与 string2 不同，则为真     [ "${myvar}" != "abc" ]
    string1 < string    如果 string1 小于 string2，则为真   [ "${myvar}" \< "abc" ]
    [[ "${myvar}" < "abc" ]]
    string1 > string    如果 string1 大于 string2，则为真   [ "${myvar}" \> "abc" ]
    [[ "${myvar}" > "abc" ]]

注意：

在字符串两边加上`""`防止出错
`<`和`>`是字符串比较，不要错用成整数比较
如果是在`[ ]`中使用`<`和`>`，需要将它们写成`\<`和`\>`

整数比较

    运算符     描述  示例
    num1 -eq num2   等于  [ 3 -eq $mynum ]
    num1 -ne num2   不等于     [ 3 -ne $mynum ]
    num1 -lt num2   小于  [ 3 -lt $mynum ]
    num1 -le num2   小于或等于   [ 3 -le $mynum ]
    num1 -ge num2   大于或等于   [ 3 -ge $mynum ]

##第三式：`&&`、`||`

`&&`可以用来对两个判断语句求与

    if [ -n "abc" ] && [ -n "aa" ]
    if [[ -n "abc" ]] && [[ -n "aa" ]]
    if test -n "abc" && test -n "aa"
    if [[ -n "abc" && -n "aa" ]]

注：只有`[[ ]]`才允许把`&&`写在里面

`||`可以用来对两个判断语句求或

    if [ -n "abc" ] || [ -n "aa" ]
    if [[ -n "abc" ]] || [[ -n "aa" ]]
    if test -n "abc" || test -n "aa"
    if [[ -n "abc" || -n "aa" ]]

注：只有`[[ ]]`才允许把`||`写在里面

小技巧

`&&`、`||`还可以用来拼接命令，达到按前一个命令成功与否来决定是否执行后一个命令的效果

    cd /data && ls         # 当`cd /data`返回0(即成功)时才执行后面的`ls`
    cd /data || cd /root   # 当`cd /data`返回非0(即失败)时才执行后面的`cd /root`

#第三招：循环
##第一式：for

    for i in {1..100}
    do
        echo ${i}
    done

注：

`{1..100}`属于通配的一种写法，展开会是`1 2 3 ... 100`（1~100以空格隔开）这样的字串。

例如`for i in 1 2 3;`这样的语句，`for`会将`1、2、3`依次赋值于`i`进行循环，而对于通配的情况，`for`则会将通配展开后将里面的每一项依次赋值于`i`进行循环。

    for i in `seq 100`
    do
        echo ${i}
    done

    for i in `seq 1 2 100`
    do
        echo ${i}
    done

注：

`seq`本身为一个命令，用于输出数字组成的序列，如`seq 100`会生成并输出`1 2 3 ... 100`（1~100以换行符隔开）这样的序列，而`seq 1 2 100`则会生成并输出`1 3 5 ... 99`（以1开始，2为公差的等差数列中小于100的项，以换行符隔开）。
反引号(\`)之间的命令会被执行，其输出结果会转换成一个变量，故上面的`for in`会依次取出`seq`的执行结果赋值于`i`进行循环。

    for ((i = 0; i < 100; i++))
    do
        echo ${i}
    done

    for ((i = 0; i < 100; i+= 2))
    do
        echo ${i}
    done

注：

以上与C语言式的`for`循环语法基本相同，区别在于双重括号：`(( ))`

##第二式：`while`、`until`

    i=0
    while [[ ${i} -lt 100 ]]
    do
        echo ${i}
        ((i++))
    done

    i=0
    until [[ ${i} -ge 100 ]]
    do
        echo ${i}
        ((i++))
    done

注：

`while`和`until`的判断原理与`if`是类似的，它会执行并它后面跟着的命令，不同点在于：

- `while`是后面语句返回值为`0`，则执行循环中的语句块，否则跳出循环;
- `until`则是后面语句返回值非`0`，则执行循环中的语句块，否则跳出循环。

#第四招：变量
##第一式：整数

整数的运算

方法较多，此处只列举最浅显易懂，并且效率最高的办法——直接将符合C语言语法的表达式放到`(( ))`中即可达到对整数的计算目的：

    echo $(( 1+1 ))        # 最简单的1+1
    echo $(( (1+2)*3/4 ))  # 表达式中还可以带括号
    echo $(( 1<<32 ))      # 左移右移也支持，但仅限于-4294967296~4294967296之间的数值
    echo $(( 1&3 ))        # &、^、|、~ 这样的位操作亦支持
    (( i=1+2 ))            # 将1+2计算出结果后赋值给i，后续若`echo ${i}`会得到3
    (( i++ ))              # 变量i自增1
    (( i+=3 ))             # 变量i自增3
    # ...                  # 还有很多，不再详列

注：

进行整数运算的方法还有：`expr`、`$[]`、`let`等shell等内置命令，也可调用**bc**、**python**等外部工具进行更复杂的数学运算

##第二式：字符串

替换

    操作                      含义
    ${string/old/new}         string中第一个old替换为new
    ${string//old/new}        string中所有old替换为new

    [casheywen@ubuntu:~]# s="i hate hate you"
    [casheywen@ubuntu:~]# echo ${s/hate/love}
    i love hate you
    [casheywen@ubuntu:~]# echo ${s//hate/love}
    i love love you

截取子串

    操作            含义
    ${string:n}     string从下标n到结尾的子串
    ${string:n:m}   string从下标n开始长度为m的子串
    ${string::m}    string从下标0开始长度为m的子串

    [casheywen@ubuntu:~]# s="0123456789"
    [casheywen@ubuntu:~]# echo ${s:3}
    3456789
    [casheywen@ubuntu:~]# echo ${s::3}
    012
    [casheywen@ubuntu:~]# echo ${s:0:3}
    012
    [casheywen@ubuntu:~]# echo ${s:2:5}
    23456

通配删除

通配删除，即按通配符，删除掉字符串中符合条件的一部分

    操作                含义
    ${string#pattern}   string从左到右删除pattern的最小通配
    ${string##pattern}  string从左到右删除pattern的最大通配
    ${string%pattern}   string从右到左删除pattern的最小通配
    ${string%%pattern}  string从右到左删除pattern的最大通配

注：

此处通配规则参考通配符一览表

最小通配和最大通配的区别：

- 最小通配：符合通配的最小子串
- 最大通配：符合通配的最大子串

例如`string`值为`/00/01/02/dir`，对于通配`/*/`，其最小通配为`/00/`，而最大通配`/00/01/02/`

    [casheywen@ubuntu:~]# s="/00/01/02/dir"
    [casheywen@ubuntu:~]# echo ${s#/*/}
    01/02/dir
    [casheywen@ubuntu:~]# echo ${s##/*/}
    dir
    [casheywen@ubuntu:~]# 
    [casheywen@ubuntu:~]# s="abc/cde/efg"
    [casheywen@ubuntu:~]# echo ${s%/*}
    abc/cde
    [casheywen@ubuntu:~]# echo ${s%%/*}
    abc
    [casheywen@ubuntu:~]# 
    [casheywen@ubuntu:~]# s="/00/01/02/dir"
    [casheywen@ubuntu:~]# echo ${s#/*/}
    01/02/dir
    [casheywen@ubuntu:~]# echo ${s##/*/}
    dir
    [casheywen@ubuntu:~]# s="abc/cde/efg"
    [casheywen@ubuntu:~]# echo ${s%/*}
    abc/cde
    [casheywen@ubuntu:~]# echo ${s%%/*}
    abc

小技巧

获取文件名：`${path##*/}` (相当于`basename`命令的功能)

获取目录名：`${path%/*}` (相当于`dirname`命令的功能)

获取后缀名：`${path##*.}`

    [casheywen@ubuntu:~]# s="/root/test/dir/subdir/abc.txt"
    [casheywen@ubuntu:~]# echo ${s##*/}
    abc.txt
    [casheywen@ubuntu:~]# echo ${s%/*}
    /root/test/dir/subdir
    [casheywen@ubuntu:~]# echo ${s##*.}
    txt

##第三式：数组

普通数组

    a=()         # 空数组
    a=(1 2 3)    # 元素为1,2,3的数组
    echo ${#a[*]}  # 数组长度
    echo ${a[2]}   # 下标为2的元素值（下标从0开始）
    a[1]=0         # 给下标为1的元素赋值

    # 遍历数组
    for i in ${a[*]}
    do
        echo ${i}
    done

    unset a        # 清空数组

关联数组

关联数组可以用于存储key-value型的数据，其功能相当于**C++**中的`map`或**python**中的`dict`。

    declare -A a        # 声明关联数组（必须有此句）
    a=(["apple"]="a1" ["banana"]="b2" ["carrot"]="c3")      # 初始化关联数组
    echo ${#a[*]}       # 获取元素个数
    echo ${a["carrot"]} # 获取元素值  
    a["durian"]="d4"    # 插入或修改元素
    echo ${!a[*]}       # 获取所有的key
    unset a["banana"]   # 删除元素

    # 遍历数组(仅value)
    for i in ${a[*]}
    do
        echo ${i}
    done

    # 遍历数组(key和value)
    for key in ${!a[*]}
    do
        echo "${key} ==> ${a[${key}]}"
    done

    unset a             # 清空数组

注：

关联数组需要bash 4.0以上版本才支持，选用需慎重。查看bash版本用`bash --version`

关联数组必须用`declare -A`显示声明类型，否则数值会出错。

##第四式：将命令执行结果存入变量

\` \` 与`$( )`

    LINE_CNT=`wc -l test.txt`

    LINE_CNT=$(wc -l test.txt)

以上命令均可把`wc -l test.txt`的结果存入`LINE_CNT`变量中

注： \` \` 和`$( )`都只将命令行标准输出的内容存入变量，如果需要将标准错误内容存入变量，需要用到重定向。

换行符处理

如果命令执行结果有多行内容，存入变量并打印时换行符会丢失：

    [casheywen@ubuntu:~]# cat test.txt
    a
    b
    c
    [casheywen@ubuntu:~]# CONTENT=`cat test.txt`
    [casheywen@ubuntu:~]# echo ${CONTENT}
    a b c

若需要保留换行符，则在打印时必须加上`""`：

    [casheywen@ubuntu:~]# CONTENT=`cat test.txt`
    [casheywen@ubuntu:~]# echo "${CONTENT}"      
    a
    b
    c

#第五招：重定向

标准输入流、标准输出流、标准错误流

    名称  英文缩写    内容  默认绑定位置  文件路径    Shell中代号
    标准输入流   stdin   程序读取的用户输入   键盘输入    /dev/stdin  0
    标准输出流   stdout  程序的打印的正常信息  终端(terminal), 即显示器  /dev/stdin  1
    标准错误流   stderr  程序的错误信息     终端(terminal)，, 即显示器     /dev/stderr     2
    重定向方式一览表
    操作  含义
    cmd > file  把 stdout 重定向到 file
    cmd >> file     把 stdout 追加到 file
    cmd 2> file     把 stderr 重定向到 file
    cmd 2>> file    把 stderr 追加到 file
    cmd &> file     把 stdout 和 stderr 重定向到 file
    cmd > file 2>&1     把 stdout 和 stderr 重定向到 file
    cmd >> file 2>&1    把 stdout 和 stderr 追加到 file
    cmd file2 cmd   cmd 以 file 作为 stdin，以 file2 作为 stdout
    cat <>file  以读写的方式打开 file
    cmd < file cmd  cmd 命令以 file 文件作为 stdin
    cmd << delimiter Here document  从 stdin 中读入，直至遇到 delimiter 分界符

##第一式：重定向标准输出流(`stdout`)

把程序打印的内容输出到文件

    # 以下两种方式都会将`Hello World`写入到hello.txt(若不存在则创建)
    echo "Hello World" > hello.txt   # hello.txt原有的将被覆盖
    echo "Hello World" >> hello.txt  # hello.txt原有内容后追加`Hello World`

##第二式：重定向标准错误流(`stderr`)

把程序的错误信息输出到文件

例如文件路径中不存在+++这个文件：

    [casheywen@ubuntu:~]# ls +++
    ls: cannot access +++: No such file or directory
    [casheywen@ubuntu:~]# ls +++ > out.txt
    ls: cannot access +++: No such file or directory

上面的`ls +++`后输出的内容为标准错误流中的错误信息，所以即使用`> out.txt`重定向标准输入，错误信息仍然被打印到了屏幕。

    # 以下两种方式都会将`ls +++`输出的错误信息输出到err.txt(若不存在则创建)
    ls +++ 2> err.txt    # err.txt原有内容将被覆盖
    ls +++ 2>> err.txt   # err.txt原有内容后追加内容

##第三式：重定向标准输入流(`stdin`)

1. 让程序从文件读取输入

    以默认从标准输入读取表达式，并进行数学计算的命令`bc`为例：

        [casheywen@ubuntu:~]# bc -q
        1+1
        2

    注：

    - `1+1`为键盘输入的内容，`2`为`bc`命令打印的计算结果
    - `bc`后的`-q`参数用于禁止输出欢迎信息
    - 以上重定向方法格式为命令 `< 文件路径`

    如果我需要把已经存在文件exp.txt中的一个表达式输入到`bc`中进行计算，可以采用重定向标准输入流的方法：

            bc -q < exp.txt

    注：

    - 当exp.txt中内容为`1+1`时，以上语句输出`2`
    - 由于`bc`命令本身支持从文件输入，如不使用重定向，也可用`bc exp.txt`达到相同效果

2. 将变量中内容作为程序输入

        EXP="1+1"
        bc -q <<< "${EXP}"

    注：

    - 以上代码等同于执行`bc`并输入`1+1`，得到的输出为`2`。
    - 以上重定向方法格式为命令 `<<< 变量内容`

3. 将当前shell脚本中的多行内容作为程序的输入

    例如在shell中内嵌多行python代码：

        python << EOF
        print 'hello from python'
        print 'hello' + 'world'
        EOF

        # 内容中支持shell变量
        MSG="shell variable"

        python << EOF
        print '${MSG}'
        EOF

    注：

    - 以上用法可以方便地将某个程序需要的多行输入内容直接包含在当前脚本中
    - 支持变量，可以动态地改变多行输入的内容
    - 以上重定向方法格式为：命令 `<< EOF (换行)...(换行) EOF`，其中的EOF换成其它字符串也是有效的，如：`命令 << ABC (换行)...(换行) ABC`的，但通用习惯都使用`EOF`

#第六招：管道
##第一式：管道的基本功能

管道的写法为 `cmd1 | cmd2`，功能是依次执行`cmd1`和`cmd2`，并将`cmd1`的标准输出作为`cmd2`的标准输入，例如：

    echo "1+1" | bc

这里 `echo "1+1"` 会将`1+1`输出到标准输出，而管道会将echo输出的`1+1`作为`bc`命令的标准输入，这样`bc`会读取到`1+1`，最终得到计算结果2打印到屏幕。

注：

- 管道可以多级拼接：`cmd1 | cmd2 | cmd3 | ...`
- 管道的返回值为最后一级命令的返回值

##第二式：管道与`while`、`read`组合

    LINE_NO=0
    cat test.txt | 
    while read LINE
    do
        (( LINE_NO++ ))
        echo "${LINE_NO} ${LINE}"
    done

    # echo "${LINE_NO}"

以上代码可以将test.txt中每一行标上行标后输出。

注：

`read`命令用于从标准输入读取一行并赋值给一个或多个变量，如`read LINE`会从标准输入读取一行并将整行内容赋值给LINE变量，`read A B`则会从标准输入读入一行并将这行的第1、2列分别赋值给A、B两个变量（分割符默认为空格或tab，可给`IFS`赋值来更改分割符）
末尾注释掉的`echo "${LINE_NO}"`若执行会输出`0`，原因是管道中的`while`语句是执行在子进程中的，不会改变父进程中`LINE_NO`变量的值

##第三式：管道与`xargs`组合

    find . -type f -name *.log | xargs rm

以上代码可以将当前目录及子目录所有后缀名为.log的文件

    find . -type f -name *.log | xargs -i mv {} /data/log

以上代码可以将当前目录及子目录中所有后缀名为.log的文件移动到/data/log中

注： `xargs`可以从标准输入读取内容，以之构建并执行另一个命令行

`xargs`直接接命令名称，则将从标准输入读取的所有内容合并为一行构建命令行并执行
`xargs`加上`-i`参数，则会每读取一行就构建并执行一个命令行，构建命令行时会将`{}`替换为该行的内容

    [casheywen@ubuntu:~]# cat test.txt
    a
    b
    c
    [casheywen@ubuntu:~]# cat test.txt | xargs echo rm 
    rm a b c
    [casheywen@ubuntu:~]# cat test.txt | xargs -i echo rm {}
    rm a
    rm b
    rm c

上例展示了`xargs`构建命令的原理，如果去掉`xargs`后的`echo`，则会执行打印出来的命令

#第七招：通配

shell通配的原理

如果你的当前目录中有`1.txt` `2.txt` `3.txt`三个文件，那么当你执行`ls *.txt`这条命令，shell究竟为你做了什么？

其实shell会先读取当前目录，然后按`*.txt`的通配条件过滤得到`1.txt` `2.txt` `3.txt`这个文件列表，然后将这个列表作为参数传给`ls`，即`ls *.txt`相当于`ls 1.txt 2.txt 3.txt`，`ls`命令本身并不会得到`*.txt`这样的参数。

注：仅当目录中没有符合`*.txt`通配的文件，shell才会将`*.txt`这个字符串当作参数直接传给`ls`命令

所以如果需要列出当前目录中所有的txt文件，我们使用`echo *.txt`也同样可以达到目的：

    [casheywen@ubuntu:~]# ls *.txt
    1.txt  2.txt  3.txt
    [casheywen@ubuntu:~]# echo *.txt
    1.txt 2.txt 3.txt

注：对于`{ }`通配shell不会读取目录并过滤获得文件列表。详细请参考下文。

通配符一览表

    字符  含义  实例
    *   匹配 0 或多个字符  a*b a与b之间可以有任意长度的任意字符, 也可以一个也没有, 如aabcb, axyzb, a012b, ab。
    ?   匹配任意一个字符    a?b a与b之间必须也只能有一个字符, 可以是任意字符, 如aab, abb, acb, a0b。
    [list]  匹配 list 中的任意单一字符    a[xyz]b a与b之间必须也只能有一个字符, 但只能是 x 或 y 或 z, 如: axb, ayb, azb。
    [!list]
    [^list]     匹配 除list 中的任意单一字符   a[!0-9]b a与b之间必须也只能有一个字符, 但不能是阿拉伯数字, 如axb, aab, a-b。
    [c1-c2]     匹配 c1-c2 中的任意单一字符 如：[0-9] [a-z]     a[0-9]b 0与9之间必须也只能有一个字符 如a0b, a1b… a9b。
    {string1,string2,...}   枚举sring1或string2(或更多)其一字符串  a{abc,xyz,123}b 展开成aabcb axyzb a123b
    {c1..c2}
    {n1..n2}    枚举c1-c2中所有字符或n1-n2中所有数字     {a..f}展开成a b c d e f
    a{1..5} 展开成a1 a2 a3 a4 a5

注：

`*`、`?`、`[ ]`的通配都会按读取目录并过滤的方式展开通配项目
`{ }`则不会有读取目录的过程，它是通过枚举所有符合条件的通配项直接展开的

    [casheywen@ubuntu:~]# ls
    1.txt  2.txt  3.txt
    [casheywen@ubuntu:~]# echo *.txt
    1.txt 2.txt 3.txt
    [casheywen@ubuntu:~]# echo {1..5}.txt
    1.txt 2.txt 3.txt 4.txt 5.txt
    [casheywen@ubuntu:~]# ls {1..5}.txt
    ls: cannot access 4.txt: No such file or directory
    ls: cannot access 5.txt: No such file or directory
    1.txt  2.txt  3.txt

由上面的命令可见，*通配的结果与目录中存在哪些文件有关系，而`{ }`的通配结果与目录中存在哪些文件无关。若用`{ }`进行通配，则有可能将不存在的文件路径作为命令行参数传给程序。

##第一式：`*`

`*`用于通配文件名或目录名中某一部分为任意内容：

    rm *.log          # 删除当前目录中所有后缀名为.log的文件
    rm */log/*.log    # 删除所有二级log目录中后缀名为.log的文件

##第二式：`[ ]`

`[ ]`用于通配文件名或目录名中某个字符为限定范围内或限定范围外的值：


    rm Program[1-9]*.log  # 删除当前目录中以Program跟着一个1到9的数字开头，并以.log为后缀名的文件
    du -sh /[^udp]*       # 对根目录中所有不以u、d、p开头的文件夹求取总大小

##第三式：`?`

`?`用于通配文件名中某处一个任意值的字符：

    rm L????.txt    # 通配一个文件名以L开头，后面跟着4个字符，并以.txt结尾的文件：如LAB01.txt

##第四式：`{ }`

`{ }`也为通配符，用于通配在它枚举范围内的值，由于它是直接展开的，我们可以将它用于批量创建目录或文件，也可以用于生成序列：

批量生成目录

    [casheywen@ubuntu:~]# ls
    [casheywen@ubuntu:~]# mkdir dir{0..2}{0..2}    
    [casheywen@ubuntu:~]# ls
    dir00  dir01  dir02  dir10  dir11  dir12  dir20  dir21  dir22

生成序列

`{ }`生成的序列常用于`for`循环

    for ip in 192.168.234.{1..255}
    do
        ping ${ip} -w 1 &> /dev/null && echo ${ip} is Alive
    done

以上例子用于查找192.168.234.1~192.168.234.255整个网段能ping通的所有ip

转载声明：
---------

> 本文由 casheywen 创作，采用 知识共享署名 3.0 中国大陆许可协议 进行许可。
> 可自由转载、引用，但需署名作者且注明文章出处。 
