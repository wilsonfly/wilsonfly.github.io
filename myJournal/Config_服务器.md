

####nfs配置：
/etc/exports

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
