# shell入门到简单 学习总结1-12章

## 第一章Shell前言：

Shell本身是一种用C语言编写的程序，从用户的角度来看，Shell是用户与Linux操作系统沟通的桥梁。用户既可以输入命令执行，又可以利用 Shell脚本编程，完成更加复杂的操作。在Linux GUI日益完善的今天在运维领域Shell编程仍然起着不可忽视的作用。深入地了解和熟练地掌握Shell编程，是每一个Linux用户的必修。

**1.1 Linux的Shell种类：Bash在日常工作中被广泛使用，同时，Bash也是大多数Linux系统默认的Shell. 经常可以看到`#!/bin/sh`，它同样也可以改为`#!/bin/bash`。**

## 第二章Shell脚本格式

**1.1 第一行#!的作用是指定该脚本程序的命令解释器：**

```bash
#!/bin/bash

echo "Hello the world"
```

**2.1 执行脚本需要添加权限和运行脚本的方式**

```bash
chmod a+x print.sh   

如果没有权限通过bash/sh方式：
bash print.sh #调用bash程序解释器脚本内容执行
sh print.sh #调用sh程序解释脚本内容并执行
```

## 第三章Shell变量

shell变量
变量是shell 传递数据的一种方法。变量是用来代表每个值的符号名。

**1.1 Shell 有两类变量：临时变量和永久变量。**

临时变量: 是shell 程序内部定义的，其使用范围仅限于定义它的程序，对其它程序不可见。
永久变量: 是环境变量，其值不随shell 脚本的执行结束而消失。

例：

```bash
[root@xuegod63 test]# echo $PATH
/usr/lib64/qt-3.3/bin:/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin:/usr/bin:/bin:/root/bin
```

**2.1 用户定义变量：由字母或下划线打头。 由字母、数字或下划线组成，并且大小写字母意义不同。变量名长度没有限制。**
引用变量值时，要在变量名前加上前缀“$”

```bash
[root@xuegod63 test]# A=aaa
# echo $A
aaa
[root@xuegod63 test]# A = aaa
bash: A: command not found
```

**3.1 位置变量和特殊变量**

位置变量：Shell解释执行用户的命令时，将命令行的第一个字作为命令名，而其它字作为参数。由出现在命令行上的位置确定的参数称为位置参数。 
位置变量：使用$N 来表示

```bash
[root@xuegod63 test]# ./example.sh file1 file2 file3
$0 这个程序的文件名 example.sh
$n 这个程序的第n个参数值，n=1..N
```

**4.1 特殊变量：**

有些变量是一开始执行Script脚本时就会设定，且不能被修改，但我们不叫它只读的系统变量，而叫它特殊变量。这些变量当一执行程序时就有了，以下是一些等殊变量：

```bash
$*    这个程序的所有参数
$#    这个程序的参数个数
$   这个程序的PID
$!  输出上一个后台程序的PID
$?  输出上一个指令的返回值
```

**5.1 自定义使用变量**

使用一个定义过的变量，只要在变量名前面加美元符号即可，如：

• 中间不能有空格，可以使用下划线（_）。
• 不能使用标点符号。
• 不能使用bash里的关键字（可用help命令查看保留关键字）。

```bash
[root@xuegod63 test]# cat expr.sh
#! /bin/sh
a=10
b=20
c=30
value1=`expr $a + $b + $c`
echo "The value of value1 is $value1"
value2=`expr $c / $b`
echo "The value of value2 is $value2"
value3=`expr $c \* $b`    #整除
echo "The value of value3 is $value3"
value4=`expr $a + $c / $b`
echo "The value of value4 is $value4"
```

测试：

```bash
[root@xuegod63 test]# ./expr.sh
The value of value1 is 60
The value of value2 is 1
The value of value3 is 600
The value of value4 is 11
```


这里在说明点：

变量名外面的花括号是可选的，加不加都行，加花括号是为了帮助解释器识别变量的边界，比如下面这种情况：


```bash
for skill in Ada Coffe Action Java
do
   echo "I am good at ${skill}Script"
done
```
如果不给skill变量加花括号，写成`echo "I am good at $skillScript"`，解释器就会把`$skill`Script当成一个变量（其值为空），代码执行结果就不是我们期望的样子了。

推荐给所有变量加上花括号，这是个好的编程习惯。 


这里在变量使用read，课外补充：

read命令接收标准输入（键盘）的输入，或者其他文件描述符的输入。得到输入后，read命令将数据放入一个标准变量中。

```bash
1.基本读取

#!/bin/bash 
echo -n "Enter your name:"           #参数-n的作用是不换行，echo默认是换行 
read  name                           #从键盘输入 
echo "hello $name, welcome to my program"   
exit 0                               #退出shell程序。

其等效于以下：
read -p "Enter your name:" name       #-p参数，允许在read命令行中直接指定一个提示
```
使用read命令存在着潜在危险。脚本很可能会停下来一直等待用户的输入。如果无论是否输入数据脚本都必须继续执行，那么可以使用 -t 选项指定一个计时器，指定read命令等待输入的秒数。当计时满时，read命令返回非零值（0为正常退出状态）; 

举例：

```bash
#!/bin/bash 
if read -t 5 -p "please enter your name:" name 
then 
echo "hello $name, welcome to my script" 
else 
echo "sorry,too slow" 
fi 
exit 0
```


## 第四章Shell特殊变量：Shell $0, $#, $*, $@, $?, $$和命令行参数

我们可以在执行 Shell 脚本时，向脚本传递参数，脚本内获取参数的格式为：`$n`。n 代表一个数字，1 为执行脚本的第一个参数，2 为执行脚本的第二个参数，以此类推……

下面是$其他参数使用方法,一般用的比较多的都是传递脚本参数：

```bash
$#	传递到脚本的参数个数
$*	以一个单字符串显示所有向脚本传递的参数。如"$*"用「"」括起来的情况、以"$1 $2 … $n"的形式输出所有参数。
$$	脚本运行的当前进程ID号
$!	后台运行的最后一个进程的ID号
$@	与$*相同，但是使用时加引号，并在引号中返回每个参数。如"$@"用「"」括起来的情况、以"$1" "$2" … "$n" 的形式输出所有参数。
$-	显示Shell使用的当前选项，与set命令功能相同。
$?	显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。
```


这个跟我们自定义变量有点区别，

看例子脚本：

```bash
#!/bin/bash
#author=yangc
#blog.yangcvo.me

echo "shell 脚本很好用1：$1";
echo "shell 脚本很好用2：$2";
echo "shell 脚本很好用3：$3";
echo "File Name: $0"
echo "First Parameter : $1"
echo "First Parameter : $2"
echo "Quoted Values: $@"
echo "Quoted Values: $*"
echo "Total Number of Parameters : $#"
```
使用脚本传递参数：

```bash
shell 脚本很好用1：3
shell 脚本很好用2：4
shell 脚本很好用3：6
File Name: sh.sh
First Parameter : 3
First Parameter : 4
Quoted Values: 3 4 6
Quoted Values: 3 4 6
Total Number of Parameters : 3
```

## 第五章 Shell替换：Shell变量替换，命令替换，转义字符

如果表达式中包含特殊字符，Shell 将会进行替换。例如，在双引号中使用变量就是一种替换，转义字符也是一种替换。

```bash
#!/bin/bash
a=10
echo -e "Value of a is $a \n"
```
运行结果：

```bash
Value of a is 10
```

这里 -e 表示对转义字符进行替换。如果不使用 -e 选项，将会原样输出：

```bash
Value of a is 10\n
```

**2.1 命令替换**

命令替换是指Shell可以先执行命令，将输出结果暂时保存，在适当的地方输出。

```bash
#!/bin/bash
DATE=`date`
echo "Date is $DATE"
USERS=`who | wc -l`
echo "Logged in user are $USERS"
UP=`date ; uptime`
echo "Uptime is $UP"
```

运行结果：

```
Date is 2017年 04月 28日 星期五 11:41:34 CST
Logged in user are 1
Uptime is 2017年 04月 28日 星期五 11:41:34 CST
11:41:34 up 266 days, 10:43,  1 user,  load average: 0.33, 0.14, 0.11
```

## 第六章Shell注释

以“#”开头的行就是注释，会被解释器忽略。
这里因为很多写脚本都带有个性的风格，这样写的更加有乐趣。

sh里没有多行注释，只能每一行加一个#号。只能像这样：

```bash
#--------------------------------------------
# 这是一个自动打ipa的脚本，基于webfrogs的ipa-build书写：
# https://github.com/webfrogs/xcode_shell/blob/master/ipa-build
# 功能：自动为etao ios app打包，产出物为14个渠道的ipa包
# 特色：全自动打包，不需要输入任何参数
#--------------------------------------------
##### 用户配置区 开始 #####
#
#
# 项目根目录，推荐将此脚本放在项目的根目录，这里就不用改了
# 应用名，确保和Xcode里Product下的target_name.app名字一致
#
##### 用户配置区 结束  #####
```

如果在开发过程中，遇到大段的代码需要临时注释起来，过一会儿又取消注释，怎么办呢？每一行加个#符号太费力了，可以把这一段要注释的代码用一对花括号括起来，定义成一个函数，没有地方调用这个函数，这块代码就不会执行，达到了和注释一样的效果。

## 第七章Shell echo命令和printf命令：格式化输出语句

echo是Shell的一个内部指令，用于在屏幕上打印出指定的字符串。命令格式：


**1.1 显示转义字符**

```bash
echo "\"It is a test\""
执行结果将是：
"It is a test"
```
**显示变量**

```bash
name="OK"
echo "$name It is a test"

执行结果将是：
OK It is a test
```

同样双引号也可以省略。

**2.1 shell 脚本中echo 颜色显示内容**

这里我因为之前在学习shell没有明确提到这个，这里我自己总结就加进来，因为很多次看到专业团队写shell跑整个脚本过程，特别舒服，开头有提示颜色说明，特别是国外哪些人写的不止好用，而且脚本跑起来也很有意思。

后面我写脚本都会该注意和提示都会有突出颜色标注。(这是个好习惯)

shell脚本中echo显示内容带颜色显示,echo显示带颜色，需要使用参数 `-e`

脚本举例：

```bash
echo -e "\033[字背景颜色；文字颜色m字符串\033[0m"
```

例如：

```bash
echo -e "\033[41;36m something here \033[0m"
```
其中41的位置代表底色， 36的位置是代表字的颜色.

⚠️注： 
　　1、字背景颜色和文字颜色之间是英文的"" 
　　2、文字颜色后面有个m 
　　3、字符串前后可以没有空格，如果有的话，输出也是同样有空格 
　　下面是相应的字和背景颜色，可以自己来尝试找出不同颜色搭配 


```bash
unset -f pathmunge
echo -e '\t\t\t\t\t\e[1;31m登录情况\e[0m\n'
last -n 6|nali
uptime
```

这里贡献下代码颜色：

```bash
　　echo -e “\033[30m 黑色字 \033[0m” 
　　echo -e “\033[31m 红色字 \033[0m” 
　　echo -e “\033[32m 绿色字 \033[0m” 
　　echo -e “\033[33m 黄色字 \033[0m” 
　　echo -e “\033[34m 蓝色字 \033[0m” 
　　echo -e “\033[35m 紫色字 \033[0m” 
　　echo -e “\033[36m 天蓝字 \033[0m” 
　　echo -e “\033[37m 白色字 \033[0m”
```

查看效果：

![](http://7xrthw.com1.z0.glb.clouddn.com/shell_01.png)

效果图

![](http://7xrthw.com1.z0.glb.clouddn.com/shell_02.png)


**3.1 printf 命令用于格式化输出， 是echo命令的增强版。它是C语言printf()库函数的一个有限的变形，并且在语法上有些不同。**

注意：printf 由 POSIX 标准所定义，移植性要比 echo 好。

举例：

```bash
# 没有引号也可以输出
$ printf %s abcdef
abcdef
# 格式只指定了一个参数，但多出的参数仍然会按照该格式输出，format-string 被重用
$ printf %s abc def
abcdef
$ printf "%s\n" abc def
abc
def
$ printf "%s %s %s\n" a b c d e f g h i j
a b c
d e f
g h i
```

## 第八章 Shell if else 判断语句

这里我要说下 这里入门说的前面几章和第八章都是经常用的比较多的。

if 语句通过关系运算符判断表达式的真假来决定执行哪个分支。Shell 有三种 if ... else 语句：
if ... fi 语句；
if ... else ... fi 语句；
if ... elif ... else ... fi 语句。


**1.1 if ... else 语句**

if ... else 语句的语法：

```bash
if [ expression ]
then
   Statement(s) to be executed if expression is true
fi
```

如果 expression 返回 true，then 后边的语句将会被执行；如果返回 false，不会执行任何语句。

最后必须以 fi 来结尾闭合 if，fi 就是 if 倒过来拼写，后面也会遇见。

注意：`expression `和方括号`([ ])`之间必须有空格，否则会有语法错误。

举个例子：

```bash
#!/bin/sh
a=10
b=20
if [ $a == $b ]
then
   echo "a is equal to b"
fi
if [ $a != $b ]
then
   echo "a is not equal to b"
fi
```
运行结果：

    a is not equal to b


**2.1文件表达式**

我们经常用到文件判断， 整数变量判断。

```bash
if [ -f  file ]    如果文件存在
if [ -d ...   ]    如果目录存在
if [ -s file  ]    如果文件存在且非空 
if [ -r file  ]    如果文件存在且可读
if [ -w file  ]    如果文件存在且可写
if [ -x file  ]    如果文件存在且可执行   
if [ -z file  ]    -z代表的是该变量是否有值。
```

举例：

```bash
# 这里的-x 参数判断$myPath是否存在并且是否具有可执行权限 
if [ ! -x "$myPath"]; then 
mkdir "$myPath" 
 fi 
 
 # 这里的-d 参数判断$myPath是否存在 
 if [ ! -d "$myPath"]; then 
 mkdir "$myPath" 
 fi 
 
 # 这里的-f参数判断$myFile是否存在 
 if [ ! -f "$myFile" ]; then 
 touch "$myFile" 
 fi 
 
 # 其他参数还有-n,-n是判断一个变量是否是否有值 
 if [ ! -n "$myVar" ]; then 
 echo "$myVar is empty" 
 exit 0 
 fi 
 
 # 两个变量判断是否相等 
 if [ "$var1" = "$var2" ]; then 
 echo '$var1 eq $var2' 
 else 
 echo '$var1 not eq $var2' 
 fi 
```
**3.1整数变量表达式**

```bash
if [ "int1" -eq "int2" ]    如果int1等于int2   
if [ "int1" -ne "int2" ]    如果不等于    
if [ "int1" -ge "int2" ]       如果>==
if [ "int1" -gt "int2" ]       如果>
if [ "int1" -le "int2" ]       如果<=
if [ "int1" -lt "int2" ]       如果<
```



if !=不等于使用

```bash
if [ $TMZONE != Asia/Shanghai ];then


这个时区不等于Asia/Shanghai  也就是没有这个名字

就\cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

if [ -f $1] 的含义:

这是一个判断语句的头半句，意思是：将一个文件普通名传给传给$1，并判断这个文件是否存在。后半句应该还有：then...，存在应该怎样做；和else...不存在应该怎样做。


举例：

```bash

[root@xuegod63 test]# cat elif.sh
#!/bin/bash

echo "input a file name:"
read file_name

if [ -d $file_name ] ; then
        echo " $file_name is a dir"
elif [ -f $file_name ] ; then
        echo " $file_name is file"
elif [ -c $file_name -o -b $file_name ] ; then
        echo " $file_name is a device file"
else
        echo " $file_name does not exist "
fi
```

## 第九章 Shell for循环


**1.1 for循环一般格式为:** 

```bash
for 变量 in 列表
do
    command1
    command2
    ...
    commandN
done
```
列表是一组值（数字、字符串等）组成的序列，每个值通过空格分隔。每循环一次，就将列表中的下一个值赋给变量。

in 列表是可选的，如果不用它，for 循环使用命令行的位置参数。

例如，顺序输出当前列表中的数字：

```bash
for loop in 1 2 3 4 5
do
    echo "The value is: $loop"
done
```
运行结果：

```bash
The value is: 1
The value is: 2
The value is: 3
The value is: 4
The value is: 5
```


这里比如举例用的比较多的 yum循环安装依赖包。

```bash
for i in \
gcc \
gcc-c++ \
vim-enhanced \
lrzsz \
ntpdate \
;
do
yum -y install $i
```

顺序输出字符串中的字符：

```bash
for str in 'This is a string'
do
    echo $str
done
```
运行结果：

```bash
This is a string
```

显示主目录下以 .bash 开头的文件：

```bash
#!/bin/bash
for FILE in $HOME/.bash*
do
   echo $FILE
done
```

运行结果：

```bash
/root/.bash_history
/root/.bash_logout
/root/.bash_profile
/root/.bashrc
```

## 第十章 Shell while循环

while循环用于不断执行一系列命令，也用于从输入文件中读取数据；命令通常为测试条件。其格式为：

以下是一个基本的while循环，测试条件是：如果COUNTER小于5，那么返回 true。COUNTER从0开始，每次循环处理时，COUNTER加1。运行上述脚本，返回数字1到5，然后终止。

```bash
COUNTER=0
while [ $COUNTER -lt 5 ]
do
    COUNTER='expr $COUNTER+1'
    echo $COUNTER
done
```

运行结果：

```bash
运行脚本，输出：
1
2
3
4
5
```
    

## 第十一章： Shell输入输出重定向：Shell Here Document，/dev/null文件

命令输出重定向的语法为：

输出重定向会覆盖文件内容，请看下面的例子：

```bash
 echo line 1 > users
$ cat users
line 1
$
```




也可以 将 Here Document 用在脚本中，例如：

```bash
#!/bin/bash
cat << EOF
+------------------------------------+
|                                    |
|   This is a simple lookup program  |
|  for good (and bad) restaurants    |
|       in Cape Town.                |
|------------------------------------+
EOF
```

运行结果：

```bash
sh test.sh
+------------------------------------+
|				                         |
|   This is a simple lookup program  |
|  for good (and bad) restaurants    |
|	   in Cape Town.	 	             |
|------------------------------------+
```

**2.1/dev/null 文件**

以前有人问过我这个/dev/null是什么意思。 这个是在shell脚本里面经常用到的。

比如说：不明白grep "tomcat" /etc/passwd>/dev/null 2>&1

 在/etc/passwd查找是否包含字符串tomcat  并把标准输出和标准错误一起重定向到/dev/null

1. 输出重定向（>）操作在命令执行发生错误时，会将错误信息直接显示到屏幕，并不记录到文件中,没必要放在内存
2. 标准输出与错误输出重定向（&>）可以将标准输出和错误输出信息一并重新定向到文件，屏幕上不会显示任何信息 ,如果没有 >/dev/null 2>&1 ，结果 就直接显示在屏幕上咯。 

grep "tomcat" /etc/passwd>/dev/null 2>&1
首先是grep "tomcat" /etc/passwd>/dev/null  将标准输出重定向到/dev/null   然后2>&1把标准错误重定向到标准输出   也就是也被重定向到了/dev/null  那结果就是标准输出和标准错误都被重定向到了/dev/null



/dev/null 是一个特殊的文件，写入到它的内容都会被丢弃；如果尝试从该文件读取内容，那么什么也读不到。但是 /dev/null 文件非常有用，将命令的输出重定向到它，会起到”禁止输出“的效果。


如果希望屏蔽 stdout 和 stderr，可以这样写：

```bash
command > /dev/null 2>&1
```


## Shell个人总结常规使用较多的一些参数语法。

我个人写shell脚本都使用到sleep参数。

##### 1.Shell脚本中让进程休眠的方法（sleep用法）

```bash
有时候写Shell的脚本，用于顺序执行一系列的程序。 有些程序在停止之后并没能立即退出，就例如有一个 tomcat 挂了，就算是用 kill -9 命令也还没瞬间就结束掉。

这么如果 shell 还没等其退出就接着执行下一行，这么就出乱子了。 刚知道了原来 shell 也能有 sleep 的参数。

复制代码 代码如下:

sleep 1 睡眠1秒
sleep 1s 睡眠1秒
sleep 1m 睡眠1分
sleep 1h 睡眠1小时
用法如下，例如重启tomcat：

复制代码 代码如下:

#!/bin/sh
/opt/tomcat/bin/shutdown.sh
sleep 3  #等3秒后执行下一条
/opt/tomcat/bin/startup.sh
```

##### 2.SHELL脚本中有exit 0，和exit 1 的区别

这里我先举个例子：

```bash
#!/bin/sh

  x=10
  if [ "$x" = 10 ]; then
    echo "is 10"
    exit 0
  fi


#!/bin/sh

  x=10
  if [ "$x" = 10 ]; then
    echo "is 10"
    exit 1
  fi
```
 当你 exit  0 的时候
在调用环境echo $? 就返回0  ，也就是说调用环境就认为 你的这个程序执行正确.
当 exit 1 的时候，一般是出错定义这个1，也可以是其他数字，很多系统程序这个错误编号是有约定的含义的。 但不为0  就表示程序运行出错。

调用环境就可以根据这个返回值判断 你这个程序运行是否ok。
如果你用 脚本a调用脚本b，要在a中判断b是否正常返回，就是根据exit 0or1来识别。
执行完b后，判断 $? 就是返回值.

##### 3.shell中获取时间

```bash
取当天时间日期赋值给DATE变量
DATE=$(date +%Y%m%d)
有时候我们需要使用今天之前或者往后的日期，这时可以使用date的 -d参数
获取明天的日期
date -d next-day +%Y%m%d
获取昨天的日期
date -d last-day +%Y%m%d
获取上个月的年和月
date -d last-month +%Y%m
获取下个月的年和月
date -d next-month +%Y%m
获取明年的年份
date -d next-year +%Y
```

##### 4.shell 过滤文件 重复如何去重

```bash
sort |uniq  用这个命令。
先 sort  排序 ，再  uniq 去重  ，要是想知道重复的数量就 用 uniq -c
这个执行看下就可以了。
```


##### 5. shell 单引号，双引号 区别与使用

```bash
单引号

str='this is a string'

单引号字符串的限制：
* 单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的；
* 单引号字串中不能出现单引号（对单引号使用转义符后也不行）。
```


script ，脚本

##### 1. 搞开发的同事让我把他写个脚本可以实现ipset.sh eth0 192.168.1.1 255.255.255.0 192.168.254.254来配置某个网卡的信息

```
#!/bin/bash
hwaddr=ifconfig |grep HWaddr |awk '{print $5}'
echo “DEVICE=$1
HWADDR=$hwaddr
TYPE=Ethernet
ONBOOT=yes
NM_CONTROLLED=yes
BOOTPROTO=static
IPADDR=$2
NETMASK=$3
GATEWAY=$4” > /etc/sysconfig/network-scripts/ifcfg-$1
service network restart
```

###### 2. shell脚本实现将文件中的几行输出合并成一行显示现在有一个文件aa.txt ,其实现格式如下，而我想实现的结果是192.168.1.17 down
```
[root@localhost shell]# cat aa.txt
192.168.1.17
down
192.168.1.103
open
192.168.1.221
open
192.168.1.104
down
192.168.1.175
down
192.168.1.112
open
192.168.1.11
down
[root@localhost shell]# awk ‘{if (NR%2==0){print $0} else {printf”%s “,$0}}’ aa.txt
192.168.1.17 down
192.168.1.103 open
192.168.1.221 open
192.168.1.104 down
192.168.1.175 down
192.168.1.112 open
192.168.1.11 down
或者
[root@localhost shell]# cat aa.txt | awk ‘{if(NR%2!=0)ORS=” “;else ORS=”\n”;print}’
192.168.1.17 down
192.168.1.103 open
192.168.1.221 open
192.168.1.104 down
192.168.1.175 down
192.168.1.112 open
192.168.1.11 down
```
但是如果aa.txt 的内容如果换种形式




## sed

##### 1. sed 取某时段内apache的访问日志
开发部有如下需求：
  * 导出 2013-05-24 15:00:00 ～ 2013-05-28 16:00:00 之间的apache访问日志
Apache日志格式为：
```
222.92.115.194 - - [28/May/2013:17:01:00 +0800] "GET /media/js/jquery.eislideshow.js HTTP/1.1" 304 -
222.92.115.194 - - [28/May/2013:17:01:00 +0800] "GET /media/style/tpl/tpl_buy_left/tpl_buy_left.js HTTP/1.1" 304 -
222.92.115.194 - - [28/May/2013:17:01:01 +0800] "GET /favicon.ico HTTP/1.1" 404 17846
222.92.115.194 - - [28/May/2013:17:01:01 +0800] "GET /large-display/interactive/ HTTP/1.1" 200 21382
222.92.115.194 - - [28/May/2013:17:01:02 +0800] "GET /favicon.ico HTTP/1.1" 404 17846
222.92.115.194 - - [28/May/2013:17:01:04 +0800] "GET /large-display/single/ HTTP/1.1" 200 21386
222.92.115.194 - - [28/May/2013:17:01:04 +0800] "GET /favicon.ico HTTP/1.1" 404 17846
222.92.115.195 - - [28/May/2013:17:01:05 +0800] "GET /dsc/ HTTP/1.1" 200 34530
222.92.115.195 - - [28/May/2013:17:01:05 +0800] "GET /media/img/channel_icon.jpg HTTP/1.1" 404 17846


截取命令：

[root@style logs]# sed -n '/24\/May\/2013:15:00:01/,/28\/May\/2013:16:59:58/p' xxxx-access_log > 20130524.15-20130528.16-access_log.txt

PS:需要注意的是如果起始时间在日志中不存在，则整个截取将返回 0 行结果。而如果结束时间在日志中不存在，则会截取到日志的最后一条。所以在截取前得要找到最日志中最合适的起始点和结束点。
我的做法是先使用grep去找到两个点  再使用sed去截取

# 找出 2013-05-24 15点第一条记录的时间
[root@style logs]# grep '24/May/2013:15' xxxx-access_log | head -1
10.200.114.183 - - [24/May/2013:15:00:01 +0800] "GET /gp10/pic_259_218_1368781965.png HTTP/1.0" 401 484
# 找出 2013-05-28 16点最后一条记录的时间
[root@style logs]# grep '28/May/2013:16' xxxx-access_log | tail -1
222.92.115.195 - - [28/May/2013:16:59:58 +0800] "GET /favicon.ico HTTP/1.1" 404 17846
# 然后取这两个时间段之间的记录
```
