

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


####在以普通用户打开的VIM当中保存一个ROOT用户文件  
:w !sudo tee %  
查阅vim的文档（输入:help :w），会提到命令:w!{cmd}，让vim执行一个外部命令{cmd}，
然后把当前缓冲区的内容从stdin传入。  
tee是一个把stdin保存到文件的小工具。  
而%，是vim当中一个只读寄存器的名字，总保存着当前编辑文件的文件路径。  


####执行上条指令
$ !!  
以SUDO运行上条命令  
$ sudo !!  

####替换上一条命令中的一个短语
$ ^foo^bar^  
这道命令的原始样式应该是这样的:  
!!:s/foo/bar/  
如果要替换掉每一个foo则应该如下这样：  
!!:gs/foo/bar

####快速备份一个文件
$ cp filename{,.bak}  
其原理就在于bash对大括号的展开操作，filename{,.bak}
这一段会被展开成filename filename.bak再传给cp，于是就有了备份的命令了  
可以尝试下

	$ echo {a,b,c}{a,b,c}{a,b,c}  
	aaa aab aac aba abb abc aca acb acc baa bab bac bba bbb bbc bca bcb bcc caa cab cac cba cbb cbc cca ccb ccc

####免密码SSH登录主机
$ ssh-copy-id remote-machine  
这个命令把当前用户的公钥串写入到远程主机的~/.ssh/authorized_keys内，这样下
次使用ssh登录的时候，远程主机就直接根据这串密钥完成身份校验，不再询问密码
了。前提是你当前用户有生成了公钥，默认是没有的，先执行ssh-keygen试试吧！
这个命令如果用手工完成，是这样的：

	your-machine$ scp ~/.ssh/identity.pub remote-machine:
	your-machine$ ssh remote-machine
	remote-machine$ cat identity.pub >> ~/.ssh/authorized_keys
如果你想删掉远程主机上的密钥，直接打开authorized_keys，搜索你的用户名，删除那
行，即可。

####清空或创建一个文件
`> file.txt`  
`>`在shell里面是标准输出重定向符，即把（前部个命令的）命令行输出转往一个文件
内，但这里没有"前部命令"，输出为空，于是就覆盖（或创建）成一个空文件了。
有些脚本的写法是:>file.txt，因为:是bash默认存在的空函数。  
单纯创建文件也可以用$touch file.txt，touch本来是用作修改文件的时间戳，但如果文件
不存在，就自动创建了。

####重置终端
reset
如果你试过不小心cat了某个二进制文件，很可能整个终端就傻掉了，可能不会换行，没
法回显，大堆乱码之类的，这时候敲入reset回车，不管命令有没有显示，就能回复正常
了。

####有关column
column 用于把输出结果进行列表格式化操作，比如mount查看挂载信息:  
mount | column -t

####通过ssh挂载远程主机上的文件夹
sshfs name@server:/path/to/folder /path/to/mount/point  
这条命令可以让你通过 SSH 加载远程主机上的文件系统为本地磁盘，前提是你需要安装
FUSE 及 sshfs 这两个软件。  
卸载的话使用 fusermount 命令：  
fusermount -u /path/to/mount/point

####执行一条命令但不保存到 history 中
`<space> command`  
通过在命令行前面添加一个空格，就可以阻止这条命令被保存到 bash history
(~/.bash_history) 文件中，这个行为可以通过 $HISTIGNORE shell 变量来控制

####显示当前目录中所有子目录的大小
du -h --max-depth=1


####查看ASCII码表
man 7 ascii  
man ascii  

####在一个子SHELL中运行一个命令
(cd /tmp && ls)  
当然这只是演示，要查看目录当然可以ls /tmp。  
好处就是不会改变当前shell的目录，以及如果命令中设计环境变量，也不会对当前
shell有任何修改。  
在Shell编程中还有很多使用上引号来括住一个命令：

	`ls /tmp`
这也是子shell过程。可是上引号的方法无法嵌套，而使用小括号的方法可以

####用TELNET看《星球大战》
telnet towel.blinkenlights.nl


####更多实例代码见https://github.com/wilsonfly/test_module/下shell目录
