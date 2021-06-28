# 1.shell简介

shell能做什么,是什么

```
批量化、自动化
Shell 是命令解释器
Shell 也是一种程序设计语言，它有变量，关键字，各种控制语句，有自己的语法结构，利用shell程序设计语言可以编写功能很强、代码简短的程序
```

shell的分类和更改

```
cat /etc/shells 
/bin/sh
/bin/bash
/sbin/nologin
/usr/bin/sh
/usr/bin/bash
/usr/sbin/nologin
/bin/tcsh
/bin/csh
```

什么时候不适合使用Shell编程：

```
1. 资源紧张的项目，特别是那些速度是重要因素的地方（排序，散序，等等）
2. 程序要进行很复杂的数学计算，特别是浮点计算，任意精度的计算，或者是复数计算
3. 要求交叉编译平台的可移植性（使用C或者是Java代替）
4. 需要结构化编程的复杂应用（需要变量类型检查和函数原型等等）
5. 对于影响系统全局性的关键任务应用。
6. 安全非常重要。你必须保证系统完整性和抵抗入侵，攻击和恶意破坏。
7. 项目由连串的依赖的各个部分组成。
8. 多种文件操作要求（Bash被限制成文件顺序存取，并且是以相当笨拙，效率低下的逐行的存取方式）
9. 需要良好的多维数组支持。
10. 需要类似链表或树这样的数据结构。
11. 需要产生或操作图象或图形用户界面。
12. 需要直接存取系统硬件。
13. 需要端口号或是socket I/O。
14. 需要使用可重用的函数库或接口。
15. 所有的私有的不开源的应用程序（Shell脚本的源代码是直接可读，能被所有人看到的

如果你需要有上面的任意一种应用，请考虑其他的更强大的脚本语言――Perl,Tcl,Python,Ruby，或者可能是其他更高级的编译型语言，例如C，C＋＋或者是Java
```



shell经常使用的元素

```
0 1 2 3+ > >>  &

[root@redhat tmp]# cat >> b.txt <<EOF
> ni hao a haha 
> EOF

[root@mail tmp]# cat b.txt 
ni hao a haha 

```



冒号与${}连用

假设a初始值为空，分析以下操作

```
[root@host haha]# ${a=ls}
a  a.txt  a.txtt  b.txt 
[root@host haha]# :  ${a=ls}

#注意：行首的冒号(:)是做什么呢，如果没有这个符号，那么在脚本中会把赋值后字符作为命令来执行
#冒号是linux中一个内置的命令，相当于true，其实是啥也不做，空命令
#冒号后的赋值作为命令的参数来解析，而不是执行
```



## linux 小技巧

bash的初始化

```
用户登录Linux时需要执行的几个文件：
/etc/profile -> (~/.bash_profile | ~/.bash_login | ~/.profile) -> ~/.bashrc -> /etc/bashrc -> ~/.bash_logout

/etc/profile--->(~/.bash_profile )  ---> ~/.bashrc  ---> /etc/bashrc

[可以配置PS1，修改登录提示符]

/etc/profile
~/.bash_profile
~/.bashrc
/etc/bashrc

登出脚本 ～/.bash_logout
```

shell编程特点

```
bash特性
    补全             tab 
    历史             history
    别名             alias
    快捷键           ^c ^a ^e esc. ^l ^d  [^insert  shift-insert] 
    前后台作业       &  fg bg jobs 
    重定向           > >> 2 &
    管道             |
    命令排序执行     
        ;   &&    ||
    通配符
            {}   ？  *
    正则表达式
    脚本
```

历史命令

```
查看历史命令
    history         /etc/profile  下的 historysize 可以修改
调用历史命令
   上下健     
   ！关键字   
   ！历史命令行号      
   !! 执行上一条命令
   ！$  上一条命令
   alt+.   
   esc    .   上一条命令的最后一个参数    
   Ctrl+r	在历史命令中查找，输入关键字调出之前的命令
   关键字+pgup/phdn  可以切换关键字相关的历史命令
                                                        
显示历史命令执行时间:
1.设置变量:
    HISTTIMEFORMAT="%Y-%m-%d %H:%M:%S"
2.再次执行history查看结果
```

Bash部分快捷键（常用）

```
	Ctrl+a 切换到命令行开始(跟home一样，但是home在某些unix环境下无法使用)
	Ctrl+e 切换到命令行末尾
	Ctrl+u 清除剪切光标之前的内容
	Ctrl+k 清除剪切光标(包括光标)之后的内容
	ctrl+y	粘贴刚才所删除的字符
	Ctrl+r	在历史命令中查找，输入关键字调出之前的命令
```







# 2.基础通配符  

```
*，?,[]，{}

例：
字符	    含义	                        实例

* 匹配 0 或多个字符	a*b  a与b之间可以有任意长度的任意字符, 也可以一个也没有, 如aabcb, axyzb, a012b, ab。

? 匹配任意一个字符	a?b  a与b之间必须也只能有一个字符, 可以是任意字符, 如aab, abb, acb, a0b。
          
[list] 匹配 list 中的任意单一字符	a[xyz]b   a与b之间必须也只能有一个字符, 但只能是 x 或 y 或 z, 如: axb, ayb, azb。
          
[!list] 匹配 除list 中的任意单一字符	a[!0-9]b  a与b之间必须也只能有一个字符, 但不能是阿拉伯数字, 如axb, aab, a-b。
          
[^list] 匹配 除list 中的任意单一字符	a[^0-9]b  a与b之间必须也只能有一个字符, 但不能是阿拉伯数字, 如axb, aab, a-b。
          
[c1-c2]	匹配 c1-c2 中的任意单一字符 如：[0-9] [a-z]	a[0-9]b  0与9之间必须也只能有一个字符 如a0b, a1b... a9b。
          
{string1,string2,...}	匹配 sring1 或 string2 (或更多)其一字符串	a{abc,xyz,123}b    a与b之间只能是abc或xyz或123这三个字符串之一。
```









# 3.shell脚本规范

1.vim helloworld.sh   ---.sh代表这个文件是个shell脚本,拓展名后缀,如果省略.sh则不易判断该文件是否为shell脚本

2.#!/usr/bin/env bash    ---shebang蛇棒, 解释器, 翻译

执行shell脚本

```
sh helloworld.sh
./helloworld.sh
bash helloworld.sh
```

bash 脚本测试

```
	•sh –x script
		这将执行该脚本并显示所有变量的值
	•sh –n script
		不执行脚本只是检查语法模式，将返回所有错误语法
	•sh –v script
		执行脚本前把脚本内容显示在屏幕上
```







# 4.变量

shell 变量是什么？ 用一个固定的字符串去表示不固定的内容

变量分类：自定义变量，环境变量，位置变量，预定义变量



## 1.自定义变量

```
定义变量：	变量名=变量值 变量名必须以字母或下划线开头，区分大小写   ip1=192.168.2.115 
引用变量：	$变量名 或 ${变量名}
查看变量：	echo $变量名  set(所有变量：包括自定义变量和环境变量)
取消变量：	unset 变量名
作用范围：	仅在当前shell中有效
```



## 2.环境变量

```
shell在开始执行时已经定义好的
env       查看所有环境变量
set       查看所有变量
环境变量拥有可继承性：export之后就拥有继承性
export   导出变量(作用范围)
临时生效
永久生效
写道4个登陆脚本中     ～/.bashrc   ~/profile                
更好放在/etc/profile.d/* 下建立独立的环境变量配置文件
			
常用环境变量：USER	UID	HOME 	   HOSTNAME	 PWD	 PS1	PATH
            
PATH：存储所有命令所在的路径
```



## 3.位置变量

```
$1 $2 $3 $4 $5 $6 $7 $8 $9 ${10}
```



## 4.预定义变量

```
$0    脚本名
$*	  所有的参数
$@ 	  所有的参数
$# 	  参数的个数
$$ 	  当前进程的PID
$!    上一个后台进程的PID
$?	  上一个命令的返回值 0表示成功	

关于在shell脚本中数组变量中 "*"跟 "@" 区别
"*"当变量加上"" 会当成一串字符串处理.
"@"变量加上"" 依然当做数组处理.
在没有加上"" 的情况下 效果是等效的.
```



示例

```
# vim test.sh
echo "第2个位置参数是$2"
echo "第1个位置参数是$1"
echo "第4个位置参数是$4"

echo "所有参数是: $*"
echo "所有参数是: $@"
echo "参数的个数是: $#"
echo "当前进程的PID是: $$"

echo '$1='$1
echo '$2='$2
echo '$3='$3
echo '$*='$*
echo '$@='$@
echo '$#='$#
echo '$$='$$
```







## 变量赋值

```
定义或引用变量时注意事项：(记得)
" "  	弱引用
' ' 	强引用

[root@tiger ~]# school=1000phone
[root@tiger ~]# echo "${school} is good"
1000phone is good
[root@tiger ~]# echo '${school} is good'
${school} is good
```



变量的赋值方式：

1.显式赋值

```
ip1=192.168.1.251
school="BeiJing 1000phone"
today1=`date +%F`
today2=$(date +%F)
```

2.read 从键盘读入变量值

```
read 变量名
read -p "提示信息: "  变量名
read -t 5 -p "提示信息: "  变量名
read -n 2 变量名
```

vim ping.sh

```
#!/usr/bin/env bash

read -p '请输入需要ping的ip: ' ip1

ping -c 2 $ip1 &>/dev/null

if [ $? = 0 ];then
	echo "网络是通的"
   else
        echo "什么垃圾网"
fi
```

```
` `  命令替换 等价于 $()   反引号中的shell命令会被先执行
```

vim memory.sh

```
#!/usr/bin/env bash

free_total=`free -h |grep '^Mem' |awk '{print $2}'`
free_use=`free -h |grep '^Mem' |awk '{print $3}'`

disk_use=`df -TH |grep '/$' |awk '{print $(NF-1)}'`

cpu_load_per=`w |grep load |awk -F':|,' '{print $8}'`

echo "内存总量为: $free_total"
echo "内存使用为: $free_use"
echo "磁盘使用率: $disk_use"
echo "cpu使用率:  $cpu_load_per"
```









## 变量运算



整数运算

```

		方法一：expr
        expr 1 + 2
		expr $num1 + $num2    			+  -  \*  /  %
		
		方法二：$(())
		echo $(($num1+$num2))      	    +  -  *  /   %
		echo $((num1+num2))
		echo $((5-3*2))	 
		echo $(((5-3)*2))
		echo $((2**3))
		sum=$((1+2)); echo $sum
		
		方法三：$[]
		echo $[5+2]						         +  -  *  /  %
		echo $[5**2]

		方法四：let
		let sum=2+3; echo $sum
		let i++; echo $i
```



小数运算

```
		echo "2*4" |bc
		echo "2^4" |bc
		echo "scale=2;6/4" |bc
		awk 'BEGIN{print 1/2}'
		echo "print 5.0/2" |python
```



浮点运算

```
		#echo "2*4" | bc
		#echo "2^4" | bc
		#echo "scale=2;6/4" | bc
			scale: 精度
		#awk 'BEGIN{print 1/2}'
		#echo "print 5.0/2" | python
```







## 变量引用

转义：\
为了显示元字符，需要引用。当一个字符被引用时，其特殊含义被禁止把有意义的变的没意义，把没意义的变的有意义

```
\n  \t  \r
# echo -e '5\\n6\n7'
5\n6
7
    

# yum groupinstall  KDE\ Plasma\ Workspaces
    
完全引用：''    //强引  硬引
部分引用：""    //弱引  软引
#ls -lh --sort=size | tac
#echo hello;world
#echo you own $1250
```

-s 选项

能够使read命令中输入的数据不显示在监视器上（实际上，数据是显示的，只是read命令将文本颜色设置成与背景相同的颜色

```
#!/bin/bash 
read  -s  -p "Enter your password: " pass 
echo "your password is $pass" 
exit 0
```

取消回显:
stty -echo
开启回显
stty echo

```
#!/bin/bash 
stty -echo
read   -p "Enter your password: " pass 
stty echo
echo "your password is $pass" 
exit 0
```







## 变量置换[扩展]

```
命令替换
	a=`date +%m%d`
	a=$(date +%m%d)
	反引号亦可用$() 代替
```







## 变量替换

一  ${parameter:-word}

```
若 parameter 为空或未设置，则用 word 代替 parameter 进行替换，parameter 的值不变[类似定义默认值]
$ a=1
$ unset b 
$ echo $a
1
$ echo $b

$ echo ${a:-3}
1
$ echo ${b:-3}
3
$ echo $a
1
$ echo $b
```



二 	${parameter:=word}

```
若 parameter 为空或未设置，则 parameter 设为值 word
$ echo $a
1
$ echo $b

$ echo ${a:=3}
1
$ echo ${b:=3}
3
$ echo $a
1
$ echo $b
3
```



三 	${parameter:+word}

```
若 parameter 设置了，则用 word 代替 parameter 进行替换，parameter 的值不变
$ echo $a
1
$ echo $b
$ echo ${a:+3}
3
$ echo $a
1
$ echo ${b:+3}

$ echo $b
```



四 	${parameter:?message}

```
若 parameter 为空或未设置，则 message 作为标准错误打印出来，这可用来检查变量是否正确设置
$ a=1
$ unset b 
$ echo ${a:?变量未设置}
1
$ echo ${b:?变量未设置}
-bash: b: 变量未设置
```



变量"内容"的删除和替换

索引及切片

```
$ a=1234567
$ echo $a
1234567
$ echo ${a:5}
67
$ echo ${a:5:1}
6
$ echo ${a:2:4}
3456
```



内容"的删除

```
${变量#关键词}				若变量内容从头开始的数据符合『关键词』，则将符合的最短数据切除 
${变量##关键词} 			若变量内容从头开始的数据符合『关键词』，则将符合的最长数据切除 
${变量%关键词} 	    		若变量内容从尾向前的数据符合『关键词』，则将符合的最短数据切除 
${变量%%关键词} 			若变量内容从尾向前的数据符合『关键词』，则将符合的最长数据切除 
${变量/旧字符串/新字符串} 	若变量内容符合『旧字符串』则『第一个旧字符串会被新字符串替代』
${变量//旧字符串/新字符串} 	若变量内容符合『旧字符串』则『全部的旧字符串会被新字符串替代』
```

```
$ a=12345671234567
$ echo $a
12345671234567
$ echo ${a#1*3}
45671234567
$ echo ${a##1*3}
4567

$ a=12345671234567
$ echo $a
12345671234567
$ echo ${a%4*7}
1234567123
```

```
$ echo ${a%%4*7}
123



$ a=12345671234567
$ echo $a
12345671234567
$ echo ${a/1/9}
92345671234567

$ echo ${a//2/8}
18345671834567
```



例：$file=/dir1/dir2/dir3/my.file.txt 

```
${file#*/}：  拿掉第一条 / 及其左边的字符串：dir1/dir2/dir3/my.file.txt 
${file##*/}： 拿掉最后一条 / 及其左边的字符串：my.file.txt 
${file#*.}：  拿掉第一个 . 及其左边的字符串：file.txt 
${file##*.}： 拿掉最后一个 . 及其左边的字符串：txt 
${file%/*}：  拿掉最后条 / 及其右边的字符串：/dir1/dir2/dir3 
${file%%/*}： 拿掉第一条 / 及其右边的字符串：(空值) 
${file%.*}：  拿掉最后一个 . 及其右边的字符串：/dir1/dir2/dir3/my.file 
${file%%.*}： 拿掉第一个 . 及其右边的字符串：/dir1/dir2/dir3/my 

${file##*/}：    对应命令 basename
${file%/*}       对应命令 dirname

记忆的方法为： 
# 是去掉左边(在键盘上 # 在 $ 之左边) 
% 是去掉右边(在键盘上 % 在 $ 之右边) 
单一符号是最小匹配﹔两个符号是最大匹配
```

```
$ a=123
$ echo ${#a}            表示$var的长度
3
```



i++和++i

对变量的值的影响：

```
[root@tiger ~]# i=1
[root@tiger ~]# let i++
[root@tiger ~]# echo $i
2
[root@tiger ~]# j=1
[root@tiger ~]# let ++j
[root@tiger ~]# echo $j
2
```



对表达式的值的影响：

```
[root@tiger ~]# unset i
[root@tiger ~]# unset j
[root@tiger ~]# 
[root@tiger ~]# i=1
[root@tiger ~]# j=1
[root@tiger ~]# 
[root@tiger ~]# let x=i++         先赋值，再运算  x=i  i=i+1
[root@tiger ~]# let y=++j         先运算，再赋值  j=j+1 y=j  
[root@tiger ~]# 
[root@tiger ~]# echo $i
2
[root@tiger ~]# echo $j
2
[root@tiger ~]# 
[root@tiger ~]# echo $x
1
[root@tiger ~]# echo $y
2
```















# 5.shell流程控制

```
	shell条件测试
	shell分支if
	shell分支case
	shell循环for
	shell循环while
	shell循环until
	shell循环控制 shift continue break exit
```



## shell条件测试

 test能够理解3种类型的表达式
	1.文件测试
	2.字符串比较
	3.数字比较

```
test
条件为真返回 0，条件为假返回 1
	
[ 条件 ]
	
 	
字符串
-n STRING
   the length of STRING is nonzero
-z STRING
   the length of STRING is zero
STRING1 = STRING2
   the strings are equal
STRING1 != STRING2
   the strings are not equal
	
数字
	eq 等于equal  ne 不等于
	ge 大于等于   gt 大于        ge  great equal    gt  great then
	le 小于等于   lt 小于        le  less  equal    lt  less then
	
文件
-f 存在且是普通文件
-d 存在且是目录
-L 存在且是符号链接
-b 块设备
-c 字符设备
-e 文件存在
-r
-w
-x
file1 -nt file2    file1 比 file2 新（修改时间mtime）   new then
file1 -ot file2    file1 比 file2 旧（修改时间）        old then
```



命令格式
格式1： test 条件表达式
格式2： [ 条件表达式 ]
格式3： [[ 条件表达式 ]]



文件测试 [ 操作符 文件或目录 ]

```
[root@tiger ~]# test -d /home
[root@tiger ~]# echo $?
0
[root@tiger ~]# test -d /home11111
[root@tiger ~]# echo $?
1
[root@tiger ~]# [ -d /home ]

[ -e dir|file ]
[ -d dir ]
[ -f file ]		是否存在，而且是文件
[ -r file ]		当前用户对该文件是否有读权限
[ -x file ]
[ -w file ]
[ -L file ]
[root@tiger ~]# [ ! -d /ccc ]  && mkdir /ccc
[root@tiger ~]# [ -d /ccc ]  || mkdir /ccc
```



数字比较 [ 整数1 操作符 整数2 ]

```
[ 1 -gt 10 ]	大于
[ 1 -lt 10 ]	小于
[ 1 -eq 10 ]	等于
[ 1 -ne 10 ]  	不等于
[ 1 -ge 10 ]	大于等于
[ 1 -le 10 ]	小于等于
[root@tiger ~]# disk_use=$(df -P |grep '/$' |awk '{print $5}' |awk -F% '{print $1}')
[root@tiger ~]# [ $disk_use -gt 90 ] && echo "war......"
[root@tiger ~]# [ $disk_use -gt 60 ] && echo "war......"
war......

[root@tiger ~]# id -u
0
[root@tiger ~]# [ $(id -u) -eq 0 ] && echo "当前是超级用户"
当前是超级用户
[alice@tiger ~]$ [ $UID -eq 0 ] && echo "当前是超级用户" || echo "you不是超级用户"
you不是超级用户
```



字符串比较

```
提示：使用双引号
[root@tiger ~]# [ "$USER" = "root" ];echo $?
0
[root@tiger ~]# [ "$USER" == "root" ];echo $?
0

[root@tiger ~]# BBB=""
[root@tiger ~]# echo ${#BBB}        // ${#var_name} 监测变量值的长度
0
[root@tiger ~]# [ -z "$BBB" ]	字符长度是为0
[root@tiger ~]# echo $?
0
[root@tiger ~]# [ -n "$BBB" ]	字符长度不为0
[root@tiger ~]# echo $?
1
```



```
[root@tiger ~]# var1=111
[root@tiger ~]# var2=
[root@tiger ~]# 							//var3变量没有定义
[root@tiger ~]# echo ${#var1}
3
[root@tiger ~]# echo ${#var2}
0
[root@tiger ~]# echo ${#var3}
0
[root@tiger ~]# [ -z "$var1" ];echo $?
1
[root@tiger ~]# [ -z "$var2" ];echo $?
0
[root@tiger ~]# [ -z "$var3" ];echo $?
0
[root@tiger ~]# [ -n "$var1" ];echo $?
0
[root@tiger ~]# [ -n "$var2" ];echo $?
1
[root@tiger ~]# [ -n "$var3" ];echo $?
1

小结：变量为空 或 未定义： 长度都为0 ！！！！！！！
```



```
root@tiger ~]# [ "$USER" = "root" ];echo $?
0
[root@tiger ~]# [ "$USER" = "alice" ];echo $?
1
[root@tiger ~]# [ "$USER" != "alice" ];echo $?
0


[root@tiger ~]# [ 1 -lt 2 -a 5 -gt 10 ];echo $?
1
[root@tiger ~]# [ 1 -lt 2  -o 5 -gt 10 ];echo $?
0

[root@tiger ~]# [[ 1 -lt 2 && 5 -gt 10 ]];echo $? 
1
[root@tiger ~]# [[ 1 -lt 2 || 5 -gt 10 ]];echo $? 
0


[root@tiger ~]# [ "$USER" = "root" ];echo $?
0
[root@tiger ~]# [ "$USER" =~ ^r ];echo $?
bash: [: =~: binary operator expected
2
[root@tiger ~]# [[ "$USER" =~ ^r ]];echo $?		//使用正则
0
```



判断变量是不是数字：

```
[root@tiger ~]# num10=123
[root@tiger ~]# num20=ssss1114ss
[root@tiger ~]# [[ "$num10" =~ ^[0-9]+$ ]];echo $?
0
[root@tiger ~]# [[ "$num20" =~ ^[0-9]+$ ]];echo $?
1
```





示例

```
1. 判断磁盘、cpu、内存是否都在合理范围内，如果负载过大则发送邮件报警
1.1 获取磁盘、cpu、内存的值
	1.1.1 磁盘使用率
		disk_use=`df -P |grep '/$' |awk '{print $5}' |awk -F% '{print $1}'`
	1.1.2 cpu locd
		cpu_lode=`uptime |awk '{print $10}' |awk -F. '{print $1}'`
	1.1.3  内存使用率
		mem_use=`echo "scale=2;`free  -m |awk 'NR==2{print $3}'`*100/`free  -m |awk 'NR==2{print $2}'`" |bc |awk -F. '{print $1}'`
1.2 和给定范围比较
	[ $disk_use -gt 90 ]
	[ $cpu_load -gt 1 ]
	[ $mem_use -gt 30 ]
1.3 如果超出范围则发送邮件[配置linux发送邮件]
	1.3.1 安装mailx
	# yum install mailx 
	1.3.2 配置邮箱
	vi /etc/mail.rc   在文件尾加上如下配置
	set from=zabbix@163.com
	set smtp=smtp.163.com
	set smtp-auth-user=zabbix@163.com
	set smtp-auth-password=邮箱授权码  OJWPPM	
	set smtp-auth=login
    1.3.3 发送邮件测试
	# echo "Content" | mail -s "Title" abc@163.com
```

vim  disk.sh

```
#!/usr/bin/env bash

#1.1.1 磁盘使用率
disk_use=`df -P |grep '/$' |awk '{print $5}' |awk -F% '{print $1}'`
#1.1.2 cpu locd
cpu_lode=`uptime |awk '{print $10}' |awk -F. '{print $1}'`
#1.1.3  内存使用率
mem_total=`free  -m |awk 'NR==2{print $2}'`
mem_use=`free  -m |awk 'NR==2{print $3}'`
mem_use_per=`echo "$mem_use*100/$mem_total" |bc `

[ $disk_use -gt 60 -o $cpu_lode -gt 1  -o $mem_use_per -gt 30 ] && echo "服务器炸了 disk_use is $disk_use, cpu_load is $cpu_lode, mem_use is $mem_use_per" | mail -s "会议纪要" tigergz2101@aliyun.com
```













## shell分支if语句



```
shell分支if语句

流程控制：if
•在一个shell脚本中的命令执行顺序称作脚本的流。大多数脚本会根据一个或多个条件来改变它们的流。
•流控制命令：能让脚本的流根据条件而改变的命令称为条件流控制命令
•exit语句：退出程序的执行，并返回一个返回码，返回码为0正常退出，非0为非正常退出，例如：
•exit 0

如果 [xx为真];则
		xxx事情
	否则
		就xxxx
结束
 

if  [];then
    cmd1
  else
    cmd2
fi


----
单分支结构
if 条件测试
then 命令序列
fi

双分支结构
if 条件测试
then 命令序列
else 命令序列
fi

多分支结构
if 条件测试1
then 命令序列

[elif 条件测试2
then 命令序列

elif 条件测试3 
then 命令序列]...

else 命令序列
fi
```



猜数游戏

```
#!/usr/bin/env bash
a=$[$RANDOM%101]

read -p '请输入一个0-100的数：' b

if [[ "$b" =~ ^[0-9]+$ ]] && [ $b -le 100 ];then
     :
   else
     echo "请输入纯数字，且为0-100之间的整数" && exit
fi

if [ $a -eq $b ];then
     echo "牛逼"
   elif [ $a -gt $b ];then
     echo "数字猜小了，实际是$a"
   else
     echo "数字猜大了，实际是$a"
fiss
```



```
RANDOM 随机数函数
a=$[$RANDOM%101]  0--100

20--50  
$[$RANDOM%31]   0-30
$[$[$RANDOM%30]+20]  20-50

多个条件联合
逻辑与
if [ $condition1 ] && [ $condition2 ]
if [ $condition -a $condition2 ]
if [[ $condition1 && $condition2 ]]

逻辑或
if [ $condition1 ] || [ $condition2 ]
if [ $condition -o $condition2 ]
if [[ $condition1 || $condition2 ]]
```











## shell分支case语句



```
case 变量 in
模式1)
 	命令序列1
 	;;
模式2)
 	命令序列2
 	;;
模式3)
 	命令序列3
	;;
*)
 	无匹配后命令序列
        ;;
esac
```



```
#!/usr/bin/env bash

cat <<-EOF
  echo "请选择你的开局"
  echo "a: 开局十连抽"
  echo "b: 是兄弟就来砍我啊"
  echo "c: 一刀999"  
EOF

read -p"请输入开局编号[a b c]: " a

case $a in
a) echo "对不起什么也没抽到，是否再来个68" 
  ;;
b) echo "有种你发位置"
  ;;
c) echo "我是渣渣辉"
  ;;
*) echo "没有这种开局，请重新开始并充值998"
  ;;
esac 
```







## shell循环for于语句



```
循环次数是固定的

for i in {取值范围}
do
	循环体
done
```



求取1-100数字的和、找出1-100中能被2整除的数字

```

#!/usr/bin/env bash

sum=0

for i in {1..100}                //for i in `seq 100`
do
   sum=$[$sum+$i]
done
echo $sum

找出1-100中能被2整除的数字
for i in {1..100}                //for i in `seq 100`
do
  a=$[$i%2]
  if [ $a -eq 0 ];then
     echo $i 
  fi
done
```



测试ip地址主机位从2到100的机器是否存活，并把存活的机器记录到文本文件alivehost.txt内。(使用ping命令)

```
#!/usr/bin/env bash                          //蛇棒
 
>alivehost.txt                               //清空文件

for i in `seq 2 100`                         //循环体开始
do
{ 
  ping -c1 -W1 10.3.143.$i &>/dev/null       //ping  -c1 ping一次  -W1 如果一秒内没有ping通则认为不通
  if [ $? -eq 0 ];then
     echo "10.3.143.$i" >>alivehost.txt
  fi
}&                                           //把ping的过程放后台执行，加快循环体
done
wait                                        //等待所有的后台进程结束

```









## shell循环while语句

```
循环次数不一定是固定的
可以固定
可以不固定

while 条件
do
	循环体
done
==当条件测试成立（条件测试为真），执行循环体
```



```
#!/usr/bin/env bash

cs=$[$RANDOM%100]

while :
do
	read -p '请输入一个数字[0--100]: ' sr
	
	if [ $cs -eq $sr ];then
	    echo "牛B" && exit
	  elif [ $cs -gt $sr ];then
	    echo "猜小了"
	  else
	    echo "猜大了"  
	
	fi
done
```



```
#!/usr/bin/env bash

a=1
sum=0
read -p '请输入一个1--100的数字: ' i
# if [ $i =~ ^[0-9]+$]   //判断是否是纯数字
while [ $a -le $i ]
do
   
    sum=$[$sum+$a]
    let a++
done

echo $sum
```







## shell循环until语句

```
循环次数不一定是固定的
可以固定
可以不固定

until 条件
do
    循环体
done
==当条件测试成立（条件测试为假），执行循环体
```

```
[root@host ~]# cat until.sh
#!/bin/bash
x=1
until [ $x -ge 10 ]
do
      echo $x
      x=`expr $x + 1`
done

x=1

while [ ! $x -ge 10 ]
do
      echo $x
      x=`expr $x + 1`
done
```







## shell循环控制shift、continue、break、exit

shift命令
位置参数可以用shift命令左移。比如shift 3表示原来的$4现在变成$1，原来的$5现在变成$2等等，原来的$1、$2、$3丢弃，$0不移动。不带参数的shift命令相当于shift 1。
对于位置变量或命令行参数，其个数必须是确定的，或者当 Shell 程序不知道其个数时，可以把所有参数一起赋值给变量$*。

```
# cat sum.sh
sum=0

until [ $# -eq 0 ]
do
    sum=`expr $sum + $1`
    shift
done
echo "sum is: $sum"

---
```

```
continue、break、exit
Linux脚本中的break continue exit return

break
结束并退出循环

continue
在循环中不执行continue下面的代码，转而进入下一轮循环

exit
退出脚本，
常带一个整数给系统，如 exit 0

return
在函数中将数据返回
或返回一个结果给调用函数的脚本

可理解为：break是立马跳出循环；continue是跳出当前条件循环，继续下一轮条件循环；exit是直接退出整个脚本
```



break示例

```
#!/bin/bash
while :
do
    echo -n "Input a number between 1 to 5: "
    read aNum
    case $aNum in
        1|2|3|4|5) echo "Your number is $aNum!"
        ;;
        *) echo "You do not select a number between 1 to 5, game is over!"
            break
           echo "break 2"
        ;;
    esac
done


echo "while done"
```



exit 示例

```
#!/bin/bash
while :
do
    echo -n "Input a number between 1 to 5: "
    read aNum
    case $aNum in
        1|2|3|4|5) echo "Your number is $aNum!"
        ;;
        *) echo "You do not select a number between 1 to 5, game is over!"
            exit 200
           echo "break 2"
        ;;
    esac
done


echo "while done"
```



continue 示例

```
#!/bin/bash
while :
do
    echo -n "Input a number between 1 to 5: "
    read aNum
    case $aNum in
        1|2|3|4|5) echo "Your number is $aNum!"
        ;;
        *) echo "You do not select a number between 1 to 5, game is over!"
           continue
           echo "break 2"
        ;;
    esac
done


echo "while done"

```



continue 、 break 、exit 区别
1. break 跳出整个循环，但是还会继续执行循环后的部分
2. exit 退出脚本，并附带返回值
3. continue 跳过本次循环的剩余部分进入下一次循环，不会退出循环、不会退出脚本











# 6.shell函数与正则



## 函数

```
 function (功能) 功能函数

定义函数
调用函数
取消函数
函数传参 

命名空间 
    local
返回值 
    return value
    value不能超过0-255
```



## shell函数function

```
函数的声明
	function_name () { 
	  list of commands
	}
	
函数名 function_name，这就是你将使用它从其他地方在你的脚本调用。	
	
unset myfunc	//取消函数

```

```
myfunc()		//函数定义
{
echo “This is my first shell function”
}
myfunc			//函数调用

产生以下执行结果
./test.sh
This is my first shell function
```



函数传参

在Shell中，调用函数时可以向其传递参数。在函数体内部，通过 $n 的形式来获取参数的值，例如，$1表示第一个参数，$2表示第二个参数

示例

```
#!/bin/bash
# author:

funParam(){
    echo "第一个参数为 $1 !"
    echo "第二个参数为 $2 !"
    echo "第十个参数为 $10 !"
    echo "第十个参数为 ${10} !"
    echo "第十一个参数为 ${11} !"
    echo "参数总数有 $# 个!"
    echo "作为一个字符串输出所有参数 $* !"
}
funParam 1 2 3 4 5 6 7 8 9 34 73
```

输出结果

```
第一个参数为 1 !
第二个参数为 2 !
第十个参数为 10 !
第十个参数为 34 !
第十一个参数为 73 !
参数总数有 11 个!
作为一个字符串输出所有参数 1 2 3 4 5 6 7 8 9 34 73 !
```



## shell编程之数组

数组中可以存放多个值。Bash Shell 只支持一维数组（不支持多维数组），初始化时不需要定义数组大小（与 PHP 类似）。
与大部分编程语言类似，数组元素的下标由0开始。
数组、数组名称、数组成员（a[0]）、下标、数组成员值。





数组定义

```
	定义方法1：
		# declare -a myarry=(5 6 7 8)
		# echo ${myarry[2]}
		显示结果为 7
	
	定义方法2：
		# array=( one two three four five six )
		# array2=(tom jack alice)
		# array3=(`cat /etc/passwd`)	       将该文件中的每一个行作为一个元素赋值给数组array3
		# array4=(`ls /var/ftp/Shell/for*`)
		# array5=(tom jack alice "bash shell")
		# colors=($red $blue $green $recolor)
		# array5=(1 2 3 4 5 6 7 "linux shell" [20]=saltstack)
		
	定义方法3：
	#!/bin/bash
	area[11]=23
	area[13]=37
	area[51]="UFOs"
		 
	#  数组成员不必一定要连贯或连续的.
	#  数组的一部分成员允许不被初始化.
	#  数组中空缺元素是允许的.
```



访问数组

当您设置任何数组变量，并可访问它，如下所示：

```
   ${array_name[index]}
```



您可以访问数组中的所有项目通过以下方式之一：

	${array_name[*]}
	${array_name[@]}
示例1

```
#!/bin/sh

NAME[0]="BJ"
NAME[1]="SH"
NAME[2]="SZ"
NAME[3]="GZ"
NAME[4]="HZ"
echo "First Index: ${NAME[*]}"
echo "Second Index: ${NAME[@]}"

输出结果
$./test.sh
First Index: BJ SH SZ GZ HZ
Second Index: BJ SH SZ GZ HZ
```

示例2

```
关于在shell脚本中数组变量中 "*"跟 "@" 区别
"*"当变量加上"" 会当成一串字符串处理.
"@"变量加上"" 依然当做数组处理.
在没有加上"" 的情况下 效果是等效的.

#!/usr/bin/env bash
array=('gz' 'cloud' '20')
echo "case 1"
for line in "${array[@]}"
do
echo $line
done

echo "case 2"
for line in "${array[*]}"
do
echo $line
done

echo "case 3"
for line in ${array[*]}
do
echo $line
done
echo "case 4"
for line in ${array[@]}
do
echo $line
done
```

输出结果

```
case 1
gz
cloud
19
case 2
gz cloud 19
case 3
gz
cloud
19
case 4
gz
cloud
19
```





正则表达式

正则表达式（regular expression, RE）是一种字符模式, 用于在查找过程中匹配指定的字符. 在大多数程序里, 正则表达式都被置于两个正斜杠之间;例如/l[oO]ve/就是由正斜杠界定的正则表达式, 它将匹配被查找的行中任何位置出现的相同模式. 在正则表达式中,元字符是最重要的概念

正则表达式与通配符:
1.正则表达式一般用于处理文本内容,常用命令有grep,sed,awk,vim等
   通配符一般用于匹配文件名,常用命令有find,ls,cp等
2.各符号的含义不尽相同.

什么地方使用正则表达式
 vim grep sed awk  nginx apache mail垃圾邮件过滤。。。  perl java python 等等都使用正则

构成  
1．元字符(基本元字符、扩展元字符)        
2．除元字符之外的任意字符都是表示他字面意思的正则表达式



 正则表达式的匹配过程

```
正则表达式是按照从表达式最左端第一个字符开始,左到右依次一个一个字符进行匹配.当字符串中有字符成功匹配到正则表达式中字符,则从这个位置开始尝试正则表达式中的下一个字符进行匹配,如果匹配成功则继续从这个位置开始匹配正则表达式中下一个字符；如果匹配不成功,则“回溯”到第一次匹配的字符处重新从正则表达式中第一个字符开始匹配。
```



正则表达式基本元字符

```
基本正则表达式元字符
元字符							  功能					 			示例	
^								行首定位符							^love	
$								行尾定位符		 					love$		
.								匹配单个字符						    l..e
*								匹配前导符0到多次					  ab*love
.*                  			任意多个字符
[]								匹配方括号中的任意一个字符				[lL]ove
[ - ]							匹配指定范围内的一个字符	    	    [a-z0-9]ove
[^]				    			匹配不在指定组内的字符					 [^a-z0-9]ove
\								用来转义元字符				   		    love\.	
\<			        			词首定位符							 \<love
\>				    			词尾定位符							 love\>


:% s/172.16.130.1/172.16.130.5/
:% s/\(172.16.130.\)1/\15/
:% s/\(172.\)\(16.\)\(130.\)1/\1\2\35/
:3,9 s/\(.*\)/#\1/

x\{m\}							字符x重复出现m次               o\{5\}
x\{m,\}		    				字符x重复出现m次以上            o\{5,\}		
x\{m,n\}						字符x重复出现m到n次				o\{5,10\}	
```

```
示例：分组  \1 代表匹配的到的第一个组
:% s/\(172.16.130.\)1/\15/                 s/\(172.16.130.\)1/\15/
:% s/\(172.\)\(16.\)\(130.\)1/\1\2\35/

:3,9 s/\(.*\)/#\1/           
```

正则表达式拓展元字符

```
扩展正则表达式元字符
+								匹配一个或多个前导字符			[a-z]+ove	
?								匹配零个或一个前导字符			lo?ve	
a|b				    			匹配a或b					 love|hate	
()								组字符loveable|rs   love(able|rs)	ov+  ov+ (ov)+
(..)(..)\1\2	    			标签匹配字符		   			(love)able\1er
x{m}			    			字符x重复m次					 o{5	
x{m,}							字符x重复至少m次				o{5,}
x{m,n}		   					字符x重复m到n次				 o{5,10}
```

```
编写正则表达式的3 个步骤：
1 知道要匹配的内容以及它如何出现在文本中。
2 编写一个模式来描述要匹配的内容
3 测试模式来查看它匹配的内容,不能错,不能漏,不能多
```

## 正则匹配示例：vim(小技巧)

```
/love/				
/^love/
/love$/
/l.ve/
/lo*ve/       
/[Ll]ove/
/love[a-z]/
/love[^a-zA-Z0-9]/

/.*/
/^$/
/^[A-Z]..$/	
/^[A-Z][a-z ]*3[0-5]/	
/[a-z]*\./	
/^ *[A-Z][a-z][a-z]$/	
/^[A-Za-z]*[^,][A-Za-z]*$/	
/\<fourth\>/		
/\<f.*th\>/		
/5{2}2{3}\./

空行
/^$/
/^[ \t]*$/	

注释行
/^#/
/^[ \t]*#/

:1,$ s/\([Oo]ccur\)ence/\1rence/	
:1,$ s/\(square\) and \(fair\)/\2 and \1/
```







# 7.shell文本处理三剑客



### shell编程之grep

grep家族

```
grep:  	在文件中全局查找指定的正则表达式，并打印所有包含该表达式的行
egrep: 	扩展的egrep，支持更多的正则表达式元字符
fgrep: 	固定grep(fixed grep)，有时也被称作快速(fast grep)，它按字面解释所有的字符
```

grep命令格式

```
grep [选项] PATTERN filename filename ...
# grep 'Tom' /etc/passwd
# grep 'bash shell' /etc/test	
找到：				       grep返回的退出状态为0
没找到：			       grep返回的退出状态为1
找不到指定文件：	        grep返回的退出状态为2

grep 程序的输入可以来自标准输入或管道，而不仅仅是文件，例如：
# grep 'tom'
# ps aux |grep 'sshd'
# ll |grep '^d'
# grep 'alice' /etc/passwd /etc/shadow /etc/group
```

grep使用的元字符

```
grep:				 使用基本元字符集	^, $, ., *, [], [^], \< \>,\(\),\{\}, \+, \|
egrep(或grep -E):	 使用扩展元字符集	?, +, { }, |, ( )
注：grep也可以使用扩展集中的元字符，仅需要对这些元字符前置一个反斜线

\w					    所有字母与数字，称为字符[a-zA-Z0-9]		'l[a-zA-Z0-9]*ve'	        'l\w*ve'
\W				        所有字母与数字之外的字符，称为非字符	'love[^a-zA-Z0-9]+' 	    'love\W+'
\b					    词边界									'\<love\>'		            '\blove\b'						
```

grep选项

```
-i, --ignore-case			 忽略大小写
-l, --files-with-matches	 只列出匹配行所在的文件名
-n, --line-number			 在每一行前面加上它在文件中的相对行号
-c, --count					 显示成功匹配的行数
-s, --no-messages			 禁止显示文件不存在或文件不可读的错误信息
-q, --quiet, --silent		 静默--quiet, --silent
-v, --invert-match			 反向查找，只显示不匹配的行
-R, -r, --recursive			 递归针对目录
--color						 颜色
-o, --only-matching			 只显示匹配的内容
-B, --before-context=NUM     匹配所在行和所在行的前num行  // 无论是ABC哪个，都要在-ABC后面加个数字，要不然搜不出来   例： egrep -B2 'lp'  /etc/passwd
-A, --after-context=NUM   	 匹配所在行和所在行的后num行
-C, --context=NUM         	 匹配所在行和所在行的前后num行
```

grep 示例

```
grep -E 或 egrep
# egrep 'NW' datafile	
# egrep 'NW' d*		
# egrep '^n' datafile
# egrep '4$' datafile
# egrep TB Savage datafile
# egrep 'TB Savage' datafile
# egrep '5\..' datafile	           
# egrep '\.5' datafile		
# egrep '^[we]' datafile
# egrep '[^0-9]' datafile	
# egrep '[A-Z][A-Z] [A-Z]' datafile
# egrep 'ss* ' datafile		
# egrep '[a-z]{9}' datafile
# egrep '\<north' datafile
# egrep '\<north\>' datafile	
# egrep '\<[a-r].*n\>' datafile
# egrep '^n\w*\W' datafile	
# egrep '\bnorth\b' datafile

# egrep 'NW|EA' datafile
# egrep '3+' datafile
# egrep '2\.?[0-9]' datafile	
# egrep '(no)+' datafile
# egrep 'S(h|u)' datafile
# egrep 'Sh|u' datafile
```

```
^   $    行首 行尾 
\< \>    词首 词尾    \<abc\>
\b 
```





### shell编程之sed

```
文本的操作
sed是一个“非交互式的”面向字符流的编辑器。
awk是一种负责模式匹配的程序设计语言，,的典型示例是将数据转换成格式化的报表。

sed  stream editor
    是一种文本编辑器,默认情况下是不会修改原文件的。
    也是一种非交互式的编辑器
    
 
工作原理
    sed 是一种在线的、非交互式的编辑器，它一次处理一行内容。处理时，把当前处理的行存储在临时缓冲区
中，称为“模式空间”（pattern space），接着用sed命令处理缓冲区中的内容，处理完成后，把缓冲区的内容
送往屏幕。接着处理下一行，这样不断重复，直到文件末尾。文件内容并没有改变，除非你使用重定向存储输出。
Sed主要用来自动编辑一个或多个文件；简化对文件的反复操作；编写转换程序等。
```

语法：

```
		sed    [选项]   '行的定位 动作'  文件...
		sed [options] 'command' file(s)
		sed [options] -f scriptfile file(s)
```

动作 

```
动作-----处理命令:   ! 非 : 放在命令前面表示取反
    1.  d    删除                                delete               
    2.  p    打印                                print
    3.  r    读取                                read              
    4.  w    写                                  write
    5.  a    追加   在匹配的行下面插入内容           append   
    6.  i    插入   在匹配行的上一行插入内容         insert
    7.  c    修改    本行替换                      change
    8.  y    转换的命令,一一对应转换
    9.  n    处理下一行                            next
    10. q    退出,不会再向模式空间读入新的行          quit
    11. s    查找替换
             '模式s/旧的内容(正则表达式)/替换内容(新的内容)/[修饰符]'
             修饰符：
                g：全局替换
                n：n为数字,1-512    替换第n个匹配到的内容
                p：打印      -n
                w：把处理过的行写入到另一个文件
     12. l   列出非打印字符

扩展:
h				把模式空间里的内容复制到暂存缓冲区(覆盖)
H				把模式空间里的内容追加到暂存缓冲区
g				取出暂存缓冲区的内容，将其复制到模式空间，覆盖该处原有内容
G				取出暂存缓冲区的内容，将其复制到模式空间，追加在原有内容后面
x				交换暂存缓冲区与模式空间的内容
```



```
注：
sed和grep不一样，不管是否找到指定的模式，它的退出状态都是0
只有当命令存在语法错误时，sed的退出状态才是非0 

支持正则表达式
与grep一样，sed在文件中查找模式时也可以使用正则表达式(RE)和各种元字符。正则表达式是括在斜杠间的模式，用于查找和替换，以下是sed支持的元字符。
使用基本元字符集	^, $, ., *, [], [^], \< \>,\(\),\{\}
使用扩展元字符集	?, +, { }, |, ( )
```

选项:

```
        -n：静默输出,关闭模式空间的输出,不会输出未匹配到的行 一般与p命令结合使用
        -e：允许进行多项编辑,也就是说对同一行做多次处理、. 可以做多点编辑  
                -e '动作1'  -e '动作2'   ==  '动作1;动作2'
        -f： 后接sed脚本,指定执行的sed脚本(将模式动作写到文件中)
        -r：允许使用扩展正则
        -i：直接作用于原文件    没有输出  在使用-i之前一定先不加-i看看效果
            -i.bak:修改原文件之前进行备份
```

控制流

```
	 !  命令取反 例: 1!d 删除第一行以外的行
	 {} 命令组合 命令用分号分隔 {1h;G} 可以理解为 -e 参数的另一种写法
	 =  打印行号(输入行的号码,而非处理的次数行号) 例如： sed -n '2{=;p}' infile
	  n  读入下一行到模式空间 例:'4{n;d}' 删除第5行
	  N  追加下一行到模式空间.
      P  输出多行模式空间的第一部分,直到第一个嵌入的换行符为止。在执行完脚本的最后一个命令之后,模式空间的内容自动输出。P命令经常出现在N命令之后和D命令之前。
	  d： 删除pattern中的所有⾏行，并读入下一新行到P中  [P来表示模式空间，M来表示保持空间]
      D：D 删除M ，P中的第一行，不读入下一行           [P来表示模式空间，M来表示保持空间]
	  这三个命令能建立一个输入. 输出循环,用来维护两行模式空间,但是一次只输出一行。
      这个循环的目的是只输出模式空间的第一行,然后返回到脚本的顶端将所有的命令应用于模式空间的第二行。没有这个循环,当执行脚本中的最后一个命令时,模式空间中的这两行都将被输出。
```

示例

```
删除命令：d
# sed -r '3d' datafile
# sed -r '3{d;}' datafile
# sed -r '3{d}' datafile

# sed -r '3,$d' datafile
# sed -r '$d' datafile
# sed -r '/north/d' datafile		
# sed -r '/sout/d' datafile

替换命令：s
# sed -r 's/west/north/g' datafile	
# sed -r 's/^west/north/' datafile	
# sed -r 's/[0-9][0-9]$/&.5/' datafile				//&代表在查找串中匹配到的内容
# sed -r 's/Hemenway/Jones/g' datafile	
# sed -r 's/(Mar)got/\1ianne/g' datafile	
# sed -r 's#3#88#g' datafile			

读文件命令：r
# sed -r '/Suan/r /etc/newfile' datafile	
# sed -r '2r /etc/hosts' a.txt		
# sed -r '/2/r /etc/hosts' a.txt

写文件命令：w
# sed -r '/north/w newfile' datafile		
# sed -r '3,$w /new1.txt' datafile

追加命令：a
# sed -r '2a\1111111111111' /etc/hosts
# sed -r '2a\1111111111111\
> 222222222222\
> 333333333333' /etc/hosts

插入命令：i
# sed -r '2i\1111111111111' /etc/hosts
# sed -r '2i111111111\
> 2222222222\
> 3333333333' /etc/hosts

修改命令：c
# sed -r '2c\1111111111111' /etc/hosts
# sed -r '2c\111111111111\
> 22222222222\
> 33333333333' /etc/hosts

获取下一行命令：n
# sed -r '/eastern/{ n; d }' datafile
# sed -r '/eastern/{ n; s/AM/Archile/ }' datafile			

暂存和取用命令：h H g G
# sed -r '1h;$G' /etc/hosts
# sed -r '1{h;d};$G' /etc/hosts
# sed -r '1h; 2,$g' /etc/hosts
# sed -r '1h; 2,3H; $G' /etc/hosts

暂存空间和模式空间互换命令：x
# sed -r '4h; 5x; 6G' /etc/hosts

反向选择: !
# sed -r '3d' /etc/hosts
# sed -r '3!d' /etc/hosts


多重编辑选项：-e
# sed -r -e '1,3d' -e 's/Hemenway/Jones/' datafile	
# sed -r '1,3d; s/Hemenway/Jones/' datafile	

# sed -r '2s/WE/1000phone/g; 2s/Gray/YYY/g' datafile
# sed -r '2{s/WE/1000phone/g; s/Gray/YYY/g}' datafile
	

七、sed常见操作
删除配置文件中#号注释行
sed -ri '/^#/d' file.conf 
sed -ri '/^[ \t]*#/d' file.conf

删除配置文件中//号注释行 
sed -ri '\Y^[ \t]*//Yd' file.conf

删除无内容空行 
sed -ri '/^[ \t]*$/d' file.conf


删除注释行及空行：
# sed -ri '/^[ \t]*#/d; /^[ \t]*$/d' /etc/vsftpd/vsftpd.conf
# sed -ri '/^[ \t]*#|^[ \t]*$/d' /etc/vsftpd/vsftpd.conf
# sed -ri '/^[ \t]*($|#)/d' /etc/vsftpd/vsftpd.conf

修改文件：
# sed -ri '$a\chroot_local_user=YES' /etc/vsftpd/vsftpd.conf
# sed -ri '/^SELINUX=/cSELINUX=disabled' /etc/selinux/config
# sed -ri '/UseDNS/cUseDNS no' /etc/ssh/sshd_config
# sed -ri '/GSSAPIAuthentication/cGSSAPIAuthentication no' /etc/ssh/sshd_config

给文件行添加注释：
# sed -r '2,6s/^/#/' a.txt
# sed -r '2,6s/(.*)/#\1/' a.txt
# sed -r '2,6s/.*/#&/' a.txt                             &匹配前面查找的内容
```



示例2

```
file1.txt
John Daggett, 341 King Road, Plymouth MA
Alice Ford, 22 East Broadway, Richmond VA
Orville Thomas, 11345 Oak Bridge Road, Tulsa OK
Terry Kalkas, 402 Lans Road, Beaver Falls PA
Eric Adams, 20 Post Road, Sudbury MA
Hubert Sims, 328A Brook Road, Roanoke VA
Amy Wilde, 334 Bayshore Pkwy, Mountain View CA
Sal Carpenter, 73 6th Street, Boston MA

用 Massachusetts 替换MA:
#sed 's/MA/Massachusetts/' file1.txt

使用多重指令:
# sed 's/ MA/, Massachusetts/ ; s/ PA/, Pennsylvania/' file1.txt
或者：
#sed -e's/ MA/, Massachusetts/' -e's/ PA/, Pennsylvania/' file1.txt

使用脚本文件:
脚本：namestate
s/ MA/, Massachusetts/
s/ PA/, Pennsylvania/
s/ CA/, California/
s/ VA/, Virginia/
s/ OK/, Oklahoma/

$ sed -f namestate file1.txt

保存输出:
$ sed -f namestate file1.txt > newfile.txt

阻止输入行自动显示:
$ sed -n 's/MA/Massachusetts/p' file1.txt

常见出错信息:
‘’不匹配
s/src/dst/ 缺少最后的“/”
```



sed流编辑器用法及解析

```
[root@host ~]# sed '1~2d' passwd 		//删除奇数行
[root@host ~]# sed '0~2d' passwd 		//删除偶数行
[root@host ~]# sed '/root/d' passwd 	//匹配到root，删除此行
[root@host ~]# sed '/root/,2d' passwd 	//匹配到root行，到此行的第2行
[root@host ~]# sed '1,2d' passwd 	    //删除文件的第1,2行

sed可以从stdin中读取内容
   # cat filename | sed 's/pattern/replace_string/'

选项`-i`会使得sed用修改后的数据替换原文件
   # sed -i 's/pattern/replace_string/' filename
   
g标记可以使sed执行全局替换
   # sed 's/pattern/replace_string/g' filename

g标记可以使sed匹配第N次以后的字符被替换
   #  echo "thisthisthisthis" | sed 's/this/THIS/2g'

sed中的分隔符可以替换成别的字符, 因为s标识会认为后面的字符为分隔符
   # sed 's:text:replace_text:'
   # sed 's|text|replace_text|'

sed可以利用指令来删除文件中的空行  
   # sed '/^$/d' filename
   # sed -r '/^[ \t]*$/d' filename

替换指定的字符串或数字
   # cat sed_data.txt
   11 abc 111 this 9 file contains 111 11 99 numbers 0000

   # sed -i 's/\b[0-9]\{3\}\b/NUMBER/g' sed_data.txt
   # cat sed_data.txt
   11 abc NUMBER this 9 file contains NUMBER 11 99 numbers 0000

由于在使用`-i`参数时比较危险, 所以我们在使用i参数时在后面加上.bak就会产生一个备份的文件,以防后悔
   # sed -i.bak 's/pattern/replace_string/' filename

sed如果在脚本中使用的话, 不可避免的要调用变量, 所以以下这种方式可以用来调用变量即''换成了""
   #  text=hello
   # echo "hello world" | sed "s/$text/HELLO/"

模式空间保持空间示例  [缓存区内默认有一个换行符]
    1. 在每行下面添加一个空行  
        sed 'G'  pass 
    2. 将第一行换到最后一行   
        sed '1{h;d};$G' pass        
    3. 交换第一行和第二行内容      
        sed '1{h;d};2G' pass
    4. 将第一行复制到每个偶数行下面   
        sed '1h;0~2G' pas
    5. 交换第一行和第三行的内容     
        sed '1{h;d};2{G;h;d};3G' pass
    6. 用sed实现tac的效果
        sed '1!G;h;$!d' pass
    7. 实现行列转化
       sed -r 'H;${x;s/\n/ /g};$!d' pass
       sed -n 'H;${x;s/\n/ /g;p}' pass
    
```















### shell编程之awk 

​	awk是行处理器: 相比较屏幕处理的优点，在处理庞大文件时不会出现内存溢出或是处理缓慢的问题，通常用来格式化文本信息
​	awk处理过程:依次对每一行进行处理，然后输出
​	默认分隔符是空格或者tab键

```
awk简介
	awk 是一种编程语言，用于在linux/unix下对文本和数据进行处理。数据可以来自标准输入、一个
或多个文件，或其它命令的输出。它支持用户自定义函数和动态正则表达式等先进功能，是linux/unix
下的一个强大编程工具。它在命令行中使用，但更多是作为脚本来使用。
	awk的处理文本和数据的方式是这样的，它逐行扫描文件，从第一行到最后一行，寻找匹配的特定
模式的行，并在这些行上进行你想要的操作。如果没有指定处理动作，则把匹配的行显示到标准输出(
屏幕)，如果没有指定模式，则所有被操作所指定的行都被处理。awk分别代表其作者姓氏的第一个字
母。因为它的作者是三个人，分别是Alfred Aho、Brian Kernighan、Peter Weinberger。gawk是awk的
GNU版本，它提供了Bell实验室和GNU的一些扩展。
```

```
awk的两种形式语法格式
awk [options] 'commands' filenames
awk [options] -f awk-script-file filenames


options：
-F   定义输入字段分隔符，默认的分隔符是空格或制表符(tab)
BEGIN{}            {}              END{}

行处理前       		行处理 	       行处理后
[root@host ~]# awk 'BEGIN{print 1/2} {print "ok"} END{print "----"}' /etc/hosts
0.5
ok
ok
ok
ok
ok
ok
[root@ccc ~]# awk 'BEGIN{print "start"}{print $1" ""ok"}END{print "stop"}' /etc/hosts
----

BEGIN{}     通常用于定义一些变量，例如BEGIN{FS=":";OFS="---"}

awk命令格式：
awk 'pattern' filename					    示例：awk -F: '/root/' /etc/passwd		
awk '{action}' filename					    示例：awk -F: '{print $1}' /etc/passwd			
awk 'pattern {action}' filename		    示例：awk -F: '/root/{print $1,$3}' /etc/passwd		
													    示例：awk 'BEGIN{FS=":"} /root/{print $1,$3}' /etc/passwd
command |awk 'pattern {action}'      示例：df -P| grep  '/' |awk '$4 > 25000 {print $4}'


awk工作原理
awk -F":" '{print $1,$3}' /etc/passwd
（1）awk使用一行作为输入，并将这一行赋给变量$0,每一行可称作为一个记录，以换行符结束
（2）然后，行被:分解成字段，每个字段存储在已编号的变量中，从$1开始
（3）awk如何知道空格来分隔字段的呢？因为有一个内部变量FS来确定字段分隔符，初始时，FS赋为空格或者是tab
（4）awk打印字段时，将以设置的方法，使用print函数打印，awk在打印的字段间加上空格，因为$1,$3间有一个,逗号。逗号比较特殊，映射为另一个变量，成为输出字段分隔符OFS，OFS默认为空格
（5）awk打印字段时，将从文件中获取另一行，并将其存储在$0中，覆盖原来的内容，然后将新的字符串分隔成字段并进行处理。该过程持续到处理文件结束。
```

awk中的特殊变量:

```
- NR: 表示记录编号, 当awk将行为记录时, 该变量相当于当前行号
- NF: 表示字段数量, 当awk将行为记录时, 该变量相当于当前列号
- $0: 表示当前记录的文本内容
- $1: 表示当前记录的第一列文本内容
- $2: 表示当前记录的第二列文本内容

RS(输入记录分隔符)        （record sign） The input record separator, by default a newline.
FS(输入字段分隔符)        （filed sign）
NR(Number of record)行数
FNR按不同的文件飞开      
ORS(输出记录分隔符)       （output record sign）The output record separator, by default a newline. 
OFS(输出字段分隔符)       （output filed sign）
NF 字段个数              （Number of filed）
```



```
FS(输入字段分隔符) （filed sign）
[root@host ~]# awk 'BEGIN{FS=":"} {print $1}' /etc/passwd
root
bin
daemon
adm
lp
sync
shutdown
halt
mail
operator
games

OFS(输出字段分隔符)    （output filed sign）
[root@host ~]# awk 'BEGIN{FS=":";OFS=".."} {print $1,$2}' /etc/passwd
root..x
bin..x
daemon..x
adm..x
lp..x
sync..x
shutdown..x

NR   表示记录编号, 当awk将行为记录时, 该变量相当于当前行号
[root@host ~]# awk -F: '{print NR,$0}' a.txt file1.txt 
1 love
2 love.
3 loove
4 looooove
5 
6 isuo
7 IPADDR=192.168.6.5
8 hjahj123
9 GATEWAY=192.168.1.1
10 NETMASK=255.255.255.0
11 DNS=114.114.114.114


FNR  表示记录编号, 当awk将行为记录时, 该变量相当于当前行号(不同文件分开)
[root@host ~]# awk -F: '{print FNR,$0}' a.txt file1.txt 
1 love
2 love.
3 loove
4 looooove
5 
1 isuo
2 IPADDR=192.168.6.5
3 hjahj123
4 GATEWAY=192.168.1.1
5 NETMASK=255.255.255.0
6 DNS=114.114.114.114
```

```
RS(输入记录分隔符)       
[root@host ~]# cat passwd 
root:x:0:0:root:/root:/bin/bashbin:x:1:1:bin:/bin:/sbin/nologindaemon:x:2:2:daemon:/sbin:/sbin/nologin
[root@host ~]# awk -F: 'BEGIN{RS="bash"} {print $0}' passwd 
root:x:0:0:root:/root:/bin/
bin:x:1:1:bin:/bin:/sbin/nologindaemon:x:2:2:daemon:/sbin:/sbin/nologin


ORS(输出记录分隔符)
[root@host ~]# cat passwd 
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin

[root@host ~]# awk -F: 'BEGIN{ORS=" "} {print $0}' passwd 
root:x:0:0:root:/root:/bin/bash bin:x:1:1:bin:/bin:/sbin/nologin daemon:x:2:2:daemon:/sbin:/sbin/nologin

练习：将文件合并为一行
[root@host ~]# awk 'BEGIN{ORS="" } {print $0}' /etc/passwd
练习：把一行内容分为多行
[root@host ~]# cat d.txt 
root:x:0:0:root:/root:/bin/bash
[root@host ~]# awk 'BEGIN{RS=":"} {print $0}' d.txt 
root
x
0
0
root
/root
/bin/bash
```



awk实用理解案例:

1. 打印一个文件中的第2列和第3列

```
$ awk '{ print $2, $3}' filename
```

2. 打印指定行指定行的某个字符

```
$ awk -F: 'NR==3{ print $7 }' /etc/passwd
```

3. 统计一个文件的行数

```
$ awk '{ print NR}' filename
```

4. 在脚本中, 传递变量到awk中

```
$ var=1000
$ echo | awk -v VARIABLE=$var '{ print VARIABLE }'
```

5. 指定字段分隔符`-F`放在`BEGIN{ FS=":" }`

```
$ awk -F: '{ print $2, $3 }' filename
$ awk 'BEGIN{ FS=":" }{ print $2, $3 }' filename
```

6. 在awk中使用for循环

```
  shell
$ grep '05/Sep/2017' cd.mobiletrain.org.log | awk '{ ips[$1]++ } END {for (i in ips){print i,ips[i]}}' | sort -k2 -rn | head -n10 

   182.140.217.111 138
   121.12.22.33 100
   10.19.3.2 90
   23.29.112.23 80
   121.31.30.189 45
   187.23.43.123 40
```

7. 在awk中使用if条件判断

```
$ awk -F: '{if($3==0) {print $1 " is administrator."}}' /etc/passwd
统计系统用户
$ awk -F":" '{if($3>0 && $3<1000){i++}} END{print i}' /etc/passwd
```





### Expect

**No.1 expect的安装**

```
[root@host ~]# yum -y install expect
```



**No.2 expect应答函数的语法**

是一个免费的编程工具, 用来实现自动的交互式任务, 而无需人为干预. 说白了`expect`就是一套用来实现自动交互功能的软件在实际工作中我们运行命令、脚本或程序时, 这些命令、脚本或程序都需要从终端输入某些继续运行的指令,而这些输入都需要人为的手工进行. 而利用`expect`则可以根据程序的提示, 模拟标准输入提供给程序, 从而实现自动化交互执行. 这就是`expect`,能够在工作中熟练的使用Shell脚本就可以很大程度的提高工作效率, 如果再搭配上expect,那么很多工作都可以自动化进行,对工作的展开如虎添翼。



```
用法：
1）定义脚本执行的shell
​	#!/usr/bin/expect 类似于#!/bin/bash

2）set timeout 30
​	设置超时时间30s

3）spawn
​	spawn是进入expect环境后才能执行的内部命令，不能直接在默认的shell环境中执行
​	功能：传递交互命令

4）expect
​	这里的expect同样是expect命令
​	功能：判断输出结果是否包含某项字符串，没有则立即返回，否则等待一段时间后返回，等待通过timeout设置

5）send
​	执行交互动作，将交互要执行的动作进行输入给交互指令
​	命令字符串结尾要加上“r”，如果出现异常等待状态可以进行核查

6）interract
​	执行完后保持交互状态，把控制权交给控制台
​	如果不加这一项，交互完成会自动退出

7）exp_continue
​	继续执行接下来的操作
```

```
expect环境中设置变量用set，识别不了bash方式定义的变量
错误方式：
[root@host expect]# cat expect02.sh 
#!/usr/bin/expect
user=22
spawn echo $user
[root@host expect]# ./expect02.sh 
invalid command name "user=22"
    while executing
"user=22"
    (file "./expect02.sh" line 3)
    
正确方式：    
[root@host expect]# cat ./expect02.sh
#!/usr/bin/expect
set user 22
spawn echo $user
[root@host expect]# ./expect02.sh 
spawn echo 22
```

ssh自动登录

```
[root@host expect]# cat expect01.sh 
#!/usr/bin/expect
spawn ssh root@192.168.62.146
expect {
        "yes/no" { send "yes\r";exp_continue }
        "password:" { send "123\r" }
}
interact
```

测试interact的作用

```
执行测试是否免交互：
要是用/usr/bin/expect的shell执行
[root@host expect]#/usr/bin/expect expect01.sh
成功

擦除最后一行interact进行测试
[root@host expect]#/usr/bin/expect expect01.sh
进入之后立即退出
```



```
\r的作用测试
[root@host expect]# cat expect01.sh 
#!/usr/bin/expect
set user root
set pass 123
set ip 192.168.62.146
spawn ssh $user@$ip
expect {
        "yes/no" { send "yes";exp_continue }
        "password:" { send "pas" }
}
interact

[root@host expect]# ./expect01.sh 
spawn ssh root@192.168.62.146
root@192.168.62.146's password: 

加上\r之后
[root@host expect]# ./expect01.sh 
spawn ssh root@192.168.62.146
root@192.168.62.146's password: 
Permission denied, please try again.
root@192.168.62.146's password: 
```

设置变量的方式

```
[root@host expect]# cat expect01.sh 
#!/usr/bin/expect
set user root
set pass 123
set ip 192.168.62.146
spawn ssh $user@$ip
expect {
        "yes/no" { send "yes\r";exp_continue }
        "password:" { send "$pass\r" }
}
interact
```

设置位置参数的方式(拓展)

```
[root@host expect]# cat expect01.sh 
#!/usr/bin/expect
set timeout 30
set user [ lindex $argv 0 ]
set pass [ lindex $argv 1 ]
set ip [ lindex $argv 2 ]
spawn ssh $user@$ip
expect {
        "yes/no" { send "yes\r";exp_continue }
        "password:" { send "$pass\r" }
}
interact
运行结果为：
[root@host expect]# ./expect01.sh root 123 192.168.62.146
spawn ssh root@192.168.62.146
root@192.168.62.146's password: 
Last login: Thu Aug 15 23:26:59 2019 from 192.168.62.136
```

将expect函数嵌入shell脚本

```
/usr/bin/expect <<-EOF
set password "123"
set username "root"
spawn ssh $username@192.168.161.130
expect "*yes/no*" {send "yes\r"}
expect "*password*" {send "$password"}
EOF
```

