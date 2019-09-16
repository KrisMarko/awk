# awk
awk基础
#awk基本应用
##概念
awk是一种优良的文本处理，linux及unix环境中现有的功能最强大的数据处理引擎之一
awk命名：Alfred V.Aho、Peter J.Weinberger、Brian W.Kernighan 三个人的姓首字母的缩写
awk ---> gawk 即:gun awk
在linux上常用的是gawk，awk是gawk的链接文件
任何awk语句都是由模式和动作组成，一个awk脚本可以有多个语句，模式决定动作语句的触发条件和触发时间
##语法结构
	awk [options] 'BEGIN{print 'start'}  'pattern{commands}' END{print 'end'}' filename
其中 BEGIN END 是awk的关键字部分，因为必须大写，这两个部分开始块和结束块是可选的

##awk工作的三个步骤
- 读
         从文件、管道或标准输入读入一行然后存到内存中
- 执行
         对每一行数据，根据awk命令按顺序执行，默认情况是处理每一行数据，也可以指定模式
- 重复
         一直重复上述两个过程直到文件结束

## awk内置变量
`$n` 是指当前记录的第n个字段，比如：`$1`表示第一个字段，`$2`表示第二个字段

	echo 'AA BBB CC' | awk '{print $1}'
		
	AA
`$0` 这个变量包含执行过程中当前行的文本内容

	echo 'AAA BBB CCC' | awk '{print $0}'

	AAA BBB CCC

`FS` 指字段的分隔符

	echo 'AAA|BBB|CCC' | awk '{print FS}'

	|
`NF` 表示字段数，在执行过程中对应于当前的行号。

	echo 'AAA BBB CCC' | awk '{print NF}'

	3

`NR` 表示记录数，在执行过程中对应于当前的行号

	echo 'AAA BBB CCC' | awk '{print NR}'

	1
`FNR` 各文件分别计数的行号，在读取多个文件的时候。NR读取完一个文件，行数继续增加而FNR重新从1开始记录。
NR == FNR ：用于在读取两个或两个以上的文件时，判断是不是在读取第一个文件。

	awk '{print FNR,$0}' a b
	
	1 AAA
	2 BBB
	3 CCC
	1 aaa
	2 bbb
	3 ccc
	
	awk '{if(FNR==NR){a[$0] = $0}else{if($0 in a){print $0}}}' test1 test2
	
`OFS` 输出字段分隔符（默认是一个空格）

	echo "AAA BBB CCC" | awk 'BEGIN{FS = " ";OFS = "---"}{print $1,$2,$3}' 

	AAA---BBB---CCC

`ORS` 输出行分隔符（默认是一个换行符），与`OFS`机制一样，对输出格式有要求的时候，可以进行格式化输出。

`RS` 输入行分隔符，判断输入部分的行的起始位置(默认是一个换行符)

		echo "AAA,BBB,CCC" | awk 'BEGIN{RS=","}{print}'

		AAA
		BBB
		CCC
		
## awk内置函数
#### 算术函数
	
|  函数名 | 说明  |
| ------------ | ------------ |
|   atan2(y,x)|  返回 y/x 的反正切 |
|  cos(x) | 返回 x 的余弦；x 是弧度  |
| sin(x)|返回 X 的正弦；x是弧度 |
|exp(x)|返回 X 幂函数|
|log(x)|返回 X 的自然对数|
|sqrt(x)|返回 X 平方根|
|int(x)|返回 X 的截断至整数的值|
|rand()|返回任意数字 n ,其中0 < = n < 1|
|srand([Expr])|将rand函数的种子值设置为Expr的参数，或如果省略Expr参数则使用某天的时间。返回先前的种子值|
####字符串函数

|	函数名	|	说明	|
| 	------------ | ------------|
|gsub( Ere, Repl, [ In ])|匹配所有字符串并替换，Ere匹配规则，Repl替换的内容，[In] 所要替换的列或者行|
|sub()|匹配第一个字符串并替换|
|toupper() | 将字符转换成大写 
|	tolower()  | 将字符转为小写|
| length()  | 长度 |
|substr( String, M, [ N ] ) |子字符串|
|match( String, Ere )| string参数为指定的字符串，Ere参数为指定的规则匹配 |
|split()|切割字符|


		awk 'BEGIN{info = "this is a test 2019";gsub(/[0-9]+/,"!",info);print info}'

		this is a test !

		awk 'BEGIN{info = "this is a test 2019"; print match(info,/[0-9]+/)?"OK":"no found"}'

		OK

		awk 'BEGIN{info = "this is a test 2019"; print substr(info,4,10)}'

		s is a test
		
		从第4个字符开始，截取10个长度字符串。

		awk 'BEGIN{info = "this is a test 2019"; print split(info,array," ")}'
		
		5
		返回的是array数组的长度，里面含有五个以" "为分隔符被切割的字符
		
#### 格式化字符输出（printf使用）
|	函数名	|	说明	|
| 	------------ | ------------|
|%d|十进制有符号整数|
|%u|十进制无符号整数|
|%f|浮点数|
|%s|字符串|
|%c|单个字符|
|%p|指针的值|
|%e|指数形式的浮点数|
|%x|%x无符号以十六进制表示的整数|
|%o|无符号以八进制表示的整数|
|%g|自动选择合适的表示法|

#### 时间函数

|	函数名	|	说明	|
| 	------------ | ------------|
|mktime(YYYY MM DD HH MM SS[DST])|生成时间格式|
|strftime([format[,timestamp]])|格式化时间输出，将时间戳转为时间字符串|
|systime()|得到时间戳，返回从1970年1月1日开始到当前时间(不计闰年)的整秒数|


		awk 'BEGIN{tstamp = mktime("2019 09 08 19 39 40"); print strftime("%c", tstamp);}'

		2019年09月08日 星期日 19时39分40秒
	
		awk 'BEGIN{tstamp1 = mktime("2019 09 08 19 39 40"); tstamp2 = mktime("2019 09 09 19 39 40"); print tstamp2 -tstamp1}'
		
		计算这2个时间段中间时间的时间差。

##awk的逻辑运算字节
|运算单元|代表意义|
|---|---|
|>|大于|
|<|小于|
|>=|大于等于|
|<=|小于等于|
|==|等于|
|!=|不等于|


		echo "AAA|BBB|333" | awk  -F'|' '{if($3 > 2){print $3}}'

		333

		echo "AAA|BBB|CCC" | awk  -F'|' '{if($1 == "AAA"){print $0}}'

		AAA|BBB|CCC

## awk的正则表达式
1、匹配表达式和正则表达式
		
		echo "AAABBBCCC" | awk '{if($0 ~/AA/) {print $0}}'

		AAABBBCCC

		echo "AAABBBCCC" | awk '{if($0 !~/DD/) {print $0}}'
		
		AAABBBCCC

		echo "AA|BB|9" | awk -F '|' '{if ($3 ~ /[0-9]/){print $3}}'

		9
			
2、关系表达式：&&（与） 、| |（或） 

		echo "AA|BB|CC" | awk -F'|' '{if($0 ~/AA/ && $2 == "BB") {print $2}}'

		BB

## 常用的命令选项：

|命令|说明|
|------|-----|
|-v|参数传递，定义和引用变量，可以把外部变量引入到内部|
|-f|指定脚本|
|-F|指定分隔符|
|-V|查看awk的版本号|
	
	
		awk -f awk-script-file input-files
		
			-f 选项加载awk-script-file中的awk脚本，input-files为输入文件名称

		cat /etc/passwd | awk -F ':' '{print $1}'
			
			打印passwd中以":"分割的第一列数据 
			
		awk -v
			shell中的变量是在awk程序中无法使用的，因为在执行awk时，是一个新的进程去处理的，因此就需要-v来向awk程序中传递参数。
			例如：shell中有一个变量  a = 15，在awk引用的时候，不能直接使用变量a。而是awk -v b=a 。这样子就可以在awk程序中使用变量b了。
			
		awk -V

## awk脚本
关于awk脚本，我们需要注意两个关键词BEGIN 和 END。

- BEGIN {这里放的是执行前的语句}
- END{这里放的是处理完所有的行后要执行的语句}
- {这里放的是处理每一行时执行的语句}

假设文件test.txt

	cat test.txt
	Lucy   001 18   
	Lily   002 19
	Alisa  003 20
	Bob    004 30
	
脚本如下

	cat test.awk
	#!/bin/awk -f
	#运行前
	BEGIN {
		
		age = 0
		
		print "NAME NO. age\n"
	}
	#运行中
	{
		age += $3
	}

	#运行后
	END {
		print "TOTAL:%10d",age
	}
	
	输出结果: awk -f test.awk test.txt
	NAME NO. age

	TOTAL:87
## 处理文件示例
1、去重

	cat test
	
	aaa 111
	bbb 222
	aaa 111
	ccc 333
	
	整行去重
	cat test | awk -F'\t' '!a[$0]++ {print }' 

	根据某一列去重
	
	cat test| awk -F'\t' '!a[$1]++ {print}'
2、计算文件大小

	ls -l *.anno | awk '{sum +=$5};END{print sum}'

	3100705

3、从文件中找出长度大于80的行

	awk 'length > 80' test

4、打印九九乘法表

	seq 9 | sed 'H;g' | awk -v RS='' '{for(i = 1 ; i< NF; i++)printf("%d x %d = %d%s",i,NR,i*NR,i==NR?"\n":";")}'
	
	1 x 2 = 2;1 x 3 = 3;2 x 3 = 6;1 x 4 = 4;2 x 4 = 8;3 x 4 = 12;1 x 5 = 5;2 x 5 = 10;3 x 5 = 15;4 x 5 = 20;1 x 6 = 6;2 x 6 = 12;3 x 6 = 18;4 x 6 = 24;5 x 6 = 30;1 x 7 = 7;2 x 7 = 14;3 x 7 = 21;4 x 7 = 28;5 x 7 = 35;6 x 7 = 42;1 x 8 = 8;2 x 8 = 16;3 x 8 = 24;4 x 8 = 32;5 x 8 = 40;6 x 8 = 48;7 x 8 = 56;1 x 9 = 9;2 x 9 = 18;3 x 9 = 27;4 x 9 = 36;5 x 9 = 45;6 x 9 = 54;7 x 9 = 63;8 x 9 = 72;
	
5、处理多文件
	
	awk '{if(NR==FNR){a[$0] = $0}else{if ($0 in a){print $0}}}' filename filename2

	获取filename2文件与fileaname文件的交集部分


## awk数组
awk 可以使用关联数组这种数据格式，索引可以是数字或者字符串。
数组使用的语法格式：
	
	array_name[index] = value

- array_name ：数组的名称
- index ：数组索引
- value：数组中元素所赋予的值

#### 创建数组

		awk 'BEGIN{array_1["baidu"]="www.baidu.com";array_2["sougou"] = "www.sougou.com";print array_1["baidu"] "\n" array_2["sougou"]}'

		www.baidu.com
		www.sougou.com
#### 删除数组元素
	
		awk 'BEGIN{array_1["baidu"]="www.baidu.com";array_2["sougou"] = "www.sougou.com";delete array_1["baidu"] ; print array_1["baidu"] "\n" array_2["sougou"]}'
		
		www.sougou.com

#### 多维数组
		
		array["0,0"] = 100;
		array["0,1"] = 200;

## awk条件语句与循环

#### 条件语句
if语句

		awk 'BEGIN{num = 10;if(num % 2 == 0)printf "%d是偶数",num}'

		10是偶数
	
if - else 语句
	
		awk 'BEGIN {num = 11; if(num %2 == 0)print "%d是偶数",num ; else printf "%d是奇数" , num}'

		11是奇数
if -else  if

#### 循环
For循环
	
	awk 'BEGIN {for(i = 0 ; i < 5 ; i++ ) print i }'

	0
	1
	2
	3
	4
While
	
	awk 'BEGIN {i = 0;while (i < 6){print i;i++}}'

	0
	1
	2
	3
	4
	5
	
Break  

	break用以结束循环
	
	awk 'BEGIN {sum = 0 ; for(i = 0;i < 10; i++){sum += i ; if(sum > 20) break ; else print "sum=", sum }}'

	sum= 0
	sum= 1
	sum= 3
	sum= 6
	sum= 10
	sum= 15

Continue

	continue 语句用于在循环内部结束本次循环，从而直接进入下一次循环迭代。

	awk 'BEGIN {for(i = 1; i <= 10 ; ++i){if(i % 2 == 0) print i ; else continue }}'
	
	2
	4
	6
	8
	10
	
Exit

	exit用于结束脚本程序的执行，该函数接受一个整数作为参数表示awk进程结束状态。如果没有提供该参数，其默认状态为0

	awk 'BEGIN {sum = 0; for(i = 0;i< 10 ; ++i){sum += i;if(sum > 20) exit(10); else print "sum = ",sum }}'

	sum =  0
	sum =  1
	sum =  3
	sum =  6
	sum =  10
	sum =  15


## awk用户自定义函数

function_name  为用户自定义的函数名称，function body 为 函数体部分，包含awk程序代码。argu1,argu2为参数

		function function_name(argu1,argu2,...)
		{

			function body
		}
## awk的几个常用高级用法
1、同时指定多个分隔符
		
		echo "AAA:|BBB:|CCC" | awk -F'[:|]' '{print $1 "\t" $3}'

		AAA   BBB
		
2、awk的key的变态用法

		echo "AAA BBB CCC 9" | awk '{a[$1 "\t" $2] += $4} END {for(item in a) printf("%s \t %d \n",item,a[item])}'

		AAA BBB 9
3、awk的范围模板

		如：awk '/root/,/mysql/' test.txt
		将显示root第一次出现到mysql第一次出现之间的所有行

4、awk的重定向
	awk 可使用shell的重定向符进行重定向输出，如：如果第一个域的值等于100，则把它输出到output_file中，也可用 >> 来重定向输出，但不清空文件，只追加
		
		seq 4 | awk '{print $0 > "a.txt"; close("a.txt")}'
		
		cat a.txt

		4


		
