

####有关字符串长度
	$echo ${#variable}
	$expr length "$variable"
	$expr "$variable" : ".*"

	$ x="this is a test"
	$ echo ${#x}
	14

	$ x="this is a test"
	$ expr length "$x"
	14

	$ x="this is a test"
	$ expr "$x" : ".*"
	14

####有关查找字符串子串位置
	$expr index "$variable" "substring"

	$ x="this is a test"
	$ expr index "$x" "is"
	3

	$ expr index "$x" "t"
	1
	(ps:如果出现重复,好象只能查到第一个,第二个,第三个,...,怎么查到呢???)

####有关得到字符串子字符串
	$echo ${variable:position:length}
	$expr substr "$variable" startposition length

	$ x="this is a test"
	$ echo ${x:1:5}
	his i

	$ x="this is a test"
	$ expr substr "$x" 1 5
	this
	(ps:注意方法一和方法二中位置的区别!)

####有关匹配正则表达式之匹配长度
	$expr match "$x" "string"

	$ x="this is a test"
	$ expr match "$x" "his"
	0
	$ expr match "$x" "this"
	4
	$ expr match "$x" "."
	1


####有关字符串的截取
1.截取头部/尾部
	
	${varible##*string} 从左向右截取最后一个string后的字符串  
	${varible#*string}从左向右截取第一个string后的字符串  
	${varible%%string*}从右向左截取最后一个string后的字符串  
	${varible%string*}从右向左截取第一个string后的字符串  
	"*"只是一个通配符可以不要
	如：
	$ MYVAR=foodforthought.jpg  
	$ echo ${MYVAR##*fo}  
	rthought.jpg  
	$ echo ${MYVAR#*fo}  
	odforthought.jpg  

	$ MYFOO="chickensoup.tar.gz"  
	$ echo ${MYFOO%%.*}  
	chickensoup  
	$ echo ${MYFOO%.*}  
	chickensoup.tar  
	
	MYFOOD="chickensoup"
	$ echo ${MYFOOD%%soup}
	chicken

2.截取中间部分字段
	
	${varible:n1:n2}:截取变量varible从n1到n2之间的字符串
	$ EXCLAIM=cowabunga
	$ echo ${EXCLAIM:0:3}
	cow
	$ echo ${EXCLAIM:3:7}
	abunga

3.使用cut截取

	cut：对标准输入的字符串进行处理
	cut -bn-m:以byte为单位，从第n个byte开始，取m个
	cut -bn,m:以byte为单位，截取第n,m个byte
	cut -b-n,m:以byte为单位，截取1-n,和第m个
	-c:以charactor为单位
	-d:指定分隔符，默认为tab
	-s:使标准输入中没有delimeter
	cut -f1:截取第1个域

	$ a=12345678
	$ echo $a|cut -b 2-5
	$ 2345 #这里是输出结果

####有关替换问题
	echo ${变量/旧的内容/新的内容} #替换一个
	echo ${变量//旧的内容/新的内容} #替换所有
	
	$ aa="No such file or directory"
	$ echo ${aa/o/a}
	Na such file or directory
	$ aa="No such file or directory"
	$ echo ${aa//o/a}
	Na such file ar directary 
