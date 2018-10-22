

####java环境搭建
1. oracle官网下载对应的jdk包：jdk-8u40-linux-i586.gz
2. 解压到指定的某个目录，比如：/usr/jdk1.8.0_40/
3. 修改环境变量，在~/.bashrc中添加：  
    export JAVA_HOME=/usr/jdk1.8.0_40  
    export JRE_HOME=${JAVA_HOME}/jre  
    export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib  
    export PATH=$PATH:$JAVA_HOME/bin:/$JAVA_HOME/jre/bin  
4. 重新登录或者source .bashrc 

####nfs配置：
sudo apt-get install nfs-kernel-server  
sudo /etc/init.d/nfs-kernel-server restart  
netstat 查看nfs端口是否已经打开，nfs端口（2049）及SUNRPC端口（111）处于监听状态  
配置/etc/exports  
/nfs/sunhuasheng/  *(rw,no_root_squash,sync)


####source找不到问题：-sh: source: bcm7584.env: file not found
---- usermod -s /bin/bash xinxian    

####DNS:
202.106.0.xx  
172.16.10.xx  

####莫名其妙的问题优先查看shell是否正常
/bin/sh -> /bin/bash  
默认/bin/sh指向dash导致登录显示异常，需要指向bash  


####默认语言切换：
/etc/default/locale   
LANG="zh_CN.UTF-8"   
LANGUAGE="zh_CN:zh"   
==>  
LANG="en_US.UTF-8"  
LANGUAGE="en_US:en"  

####iscsi target 挂载硬盘：
server：192.17.2.xx，本机另外一块网卡：192.17.2.xx  
root@ubuntu:/# sudo iscsiadm -m discovery -t sendtargets -p 192.17.2.xx
192.17.2.xx:3260,1 iqn.2001-05.com.equallogic:0-af1ff6-ae29e38d1-24600000034525c0-build-1  
root@ubuntu:/#  
root@ubuntu:/# sudo iscsiadm -m node --targetname iqn.2001-05.com.equallogic:0-af1ff6-ae29e38d1-24600000034525c0-build-1 -p 192.17.2.250 --login
Logging in to [iface: default, target: iqn.2001-05.com.equallogic:0-af1ff6-ae29e38d1-24600000034525c0-build-1, portal: 192.17.2.250,3260]
Login to [iface: default, target: iqn.2001-05.com.equallogic:0-af1ff6-ae29e38d1-24600000034525c0-build-1, portal: 192.17.2.250,3260]: successful
root@ubuntu:/# mount /dev/sdb1  /exdata -t ext3  
更多详情见：[ubuntu 12.04中iscsi target和initiator的安装和使用-liujunwei1234-ChinaUnix博客](http://blog.chinaunix.net/uid-20940095-id-3487049.html)  

####源：
/etc/apt/source.list  
目前用的是sources.list_120  

####版本号：
ubuntu 版本号：cat /etc/lsb-release  
linux 版本号：cat /proc/version ;   uname -a  

####命令行冠首问题：
/etc/hosts ,/etc/hostname 可配对应host  
/etc/profile  
/etc/bash.bashrc  
修改PS1环境变量  

####时区问题
复制相应的时区文件，替换系统时区文件；或者创建链接文件  
  cp /usr/share/zoneinfo/$主时区/$次时区 /etc/localtime  
  在中国可以使用：  
  cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime  

####root用户组：
/etc/sudoers

####服务器名称：
dell r710  172.16.11.xx  
dell 2950  172.16.11.xx

####添加用户默认文件：
/etc/skel 下.bash_logout  .bashrc       .profile  

####添加新用户
sudo useradd  bianguangrong  -d /home/bianguangrong   
chown bianguangrong:bianguangrong /home/bianguangrong
