





####(0209)版本控制流程梳理：
评审、研发自测试、转版本发布、基线问题单管理  
**评审环节：**  
1、发布评审时需要通知到 "平台项目负责人"+"模块组组长"，并且两人全部"+1 Looks good to me, but someone else must approve”后，自己选择 "+2 Looks good to me, approved"，并选择 "Publish and Submit" 上传代码。  
2、评审时间需要在发布评审后，两个工作日内必须完成整个评审的过程。 这个过程的责任人为评审发布人。  
3、紧急情况下可以先自己完成评审，并必须要同时发布紧急邮件评审通知，通知到各个负责人。注意要必须通知。  
4、Git log说明要清晰易懂。  
5、在各个模块负责人修改问题上传代码时同步上传并评审ReleaseNotes.txt，请各个评审人注意看是否有上传ReleaseNotes.txt文件，如发现没有请拒绝评审。  
**研发自测试：**  
1、代码提交后自己操作CI完成大系统编译。  
2、完成编译后完成研发自测试，确保编译后的文件解决了相关问题。  
3、为了确保研发基线发布的有效性，请大家务必重视研发自测试环节。会在基线测试时评估问题回归的有效性。  
**转版本发布：**  
1、当前基线版本每两周转一个版本。暂定于周五上午研发内部发布。  
2、研发自测试后周一转测试部。  
**转版本发布交付件：**  
1、Release Note  
2、朝歌Android机顶盒基线特性列表    
**Release Note格式如最后**
**基线问题单管理：**  
1、每两周过一次基线问题单，会同步要求基线发布版本上要解决哪些问题，要合入哪些问题。  
2、基线问题单的解决效率。要在每一个版本上确定要解决掉哪些问题，在发布版本时也要确保已经解决这些问题。  
**奖惩制度的建立，几个关键点：**  
1、评审环节不允许自己评审自己(除了发紧急邮件通知外)，是否符合要求，需要人定期检查。  
2、确保自测试，未回归通过问题的回溯。  
3、基线问题单的解决率。  
**Release Notes:**  
文件介绍：此文件记录大系统所有的修改；
最新的修改填写到Version版本号最高的一栏中，比如有V1.1和V1.0，就要填到V1.1中的最后部分；
问题的前面的序列号为累加的数字；
/* 模板 */
序列号. 修改问题的简要描述
责任人: （自己的姓名）
修改描述: （填写详细描述）
修改文件: （修改的源文件）
更新的库或可执行文件:
影响范围:
测试建议:
  
  
####(0121)3798M单板待机调试
1、直接在串口上配置himm 0xf80000bc 0x80510001，看是否能进入待机（不会有suspend的打印，可以量一下电压有没有），是否能唤醒；  
2、在串口上配置himm 0xf840e520 0x11111111，同样看看是否可以进入待机，是否能唤醒 
提供的朝歌的镜像烧写到demo1a的高安单板上，发现同样无法唤醒。进一步定位发现，android系统启动后，c51里的代码全部被擦除了：   
![pic_004](res/Accumulating/accumulating_004.png)  
而在boot下，c51的数据是存在的：  
![pic_003](res/Accumulating/accumulating_003.png)  


####(1231)有关sizeof(更多详情见 [sizeof，终极无惑（上）](http://blog.csdn.net/freefalcon/article/details/54839))
编译器的pack指令，它是用来调整结构体对齐方式的，不同编译器名称和用法略有不同，VC6中通过`#pragma pack`实现，也可以直接修改/Zp编译开关。#pragma pack的基本用法为：`#pragma pack( n )`，n为字节对齐数，其取值为1、2、4、8、16，默认是8，如果这个值比结构体成员的sizeof值小，那么该成员的偏移量应该以此值为准，即是说，结构体成员的偏移量应该取二者的最小值，公式如下：
offsetof( item ) = min( n, sizeof( item ) )
再看示例：

    #pragma pack(push) // 将当前pack设置压栈保存
    #pragma pack(2) // 必须在结构体定义之前使用
    struct S1
    {
    char c;
    int i;
    };
    struct S3
    {
    char c1;
    S1 s;
    char c2
    };
    #pragma pack(pop) // 恢复先前的pack设置
       
计算sizeof(S1)时，min(2, sizeof(i))的值为2，所以i的偏移量为2，加上sizeof(i)等于6，能够被2整除，所以整个S1的大小为6。  
同样，对于sizeof(S3)，s的偏移量为2，c2的偏移量为8，加上sizeof(c2)等于9，不能被2整除，添加一个填充字节，所以sizeof(S3)等于10。  
还有一点要注意，**“空结构体”（不含数据成员）的大小不为0，而是1**。试想一个“不占空间”的变量如何被取地址、两个不同的“空结构体”变量又如何得以区分呢？于是，“空结构体”变量也得被存储，这样编译器也就只能为其分配一个字节的空间用于占位了。如下：  

    struct S5 { };
    sizeof( S5 ); // 结果为1

  

####(1214)python 自动补全
python可以Tab键补全，可以在启动python后，执行下
import readline, rlcompleter; readline.parse_and_bind("tab: complete")
这就可以按 Tab 键补全了。  
python 如果嫌每次都要键入这东西麻烦的话，可以把上边这行写到 ~/.pythonstartup.py ，在 ~/.bashrc 里加一个环境变量
export PYTHONSTARTUP=~/.pythonstartup.py
这就会每次启动 python 都先执行了


####(1126)有关data分区概率性变成只读问题
目前在测试过程中出现一台盒子data分区变成ro(目前使用的ext4文件系统格式化策略是出现冲突等异常后,只读挂载分区,用于保护用户数据)。这个问题在反复上下电的情况下,比较容易出现  
讨论后处理方法:  
1.在后台卸载userdata分区,使用e2fsck进行修复,再重新挂载.但是userdata卸载以后,launcher/ 虚拟机等进程会出现异常   
2.每次开机后都对userdata进行检查并修复  
选用第二种方法，并且在修复后仍然挂载不上的情况将data分区格式化掉。  
![pic_007](res/Accumulating/accumulating_007.png)    
![pic_008](res/Accumulating/accumulating_008.png)    


####putty作串口工具出现过不能输入情况，换用secreCRT就ok，wtf！

####build.prop 本身的属性会影响属性的解析
属性设置失败：  
-rw-rw-rw- root     root         2909 2014-11-18 18:01 build.prop  
属性设置成功：  
-rw-r--r-- root     root         2909 2014-11-18 18:01 build.prop  

####查看某个进程内存使用情况：
procmem  one_pid  

####查看进程中具体某个线程占cpu：
top -t -m 5  

####cts，provider项：
run cts -p android.provider  

####logcat抓取日志，剔除某个tag的日志：
logcat |grep -v SettingsProvide  
grep -v 'Copy_Project_Images_Files\|Rm_unnecessary_files' project_priv.sh

####安全红线查找权限超规格文件：
1、adb shell  
2、busybox find \( -perm -04000 -o -perm -02000 \) -exec busybox ls -ld {} \; > /data/finds.txt  
3、退出adb，执行adb pull /data/finds.txt d:\finds.txt，检查此txt文件中内容  

####网络部分朝歌自己代码：
ethernet/java/android/net/ethernet/EthernetManager.java  
ethernet/java/android/net/ethernet/EthernetManager.java  
core/java/android/net/EthernetDataTracker.java  

####bootanimation.zip 压缩:
 zip -r -X -Z store ../bootanimation.zip  part*/*.png desc.txt  

####wifi mac:
iwpriv wlan0 efuse_set mac,00076322334a  
iwpriv wlan0 efuse_get mac  

####hisi usb3.0：
关闭usb3.0的方法：在device\hisilicon\bigfish\sdk\source\kernel\linux-3.10.y目录 device drivers-->usb support--->xhci hcd  
--对应xhci-hcd.ko  

####网口半双工/全双工配置：
 查询命令：  
ethtool eth0  
全双工、100M、不自动协商  
ethtool -s eth0 autoneg off speed 100 duplex full    
半双工、100M、不自动协商  
ethtool -s eth0 autoneg off speed 100 duplex half  
全双工、10M、不自动协商  
ethtool -s eth0 autoneg off speed 10 duplex full  
半双工、10M、不自动协商  
ethtool -s eth0 autoneg off speed 10 duplex half  
自动协商  
ethtool -s eth0 autoneg on  

####获取设备vendor、product：
root@android:/ # cat /proc/bus/input/devices                                    
I: Bus=0000 Vendor=0001 Product=0001 Version=0100  
可以对应到  

####Linux/Android库的识别方法：
识别Android/Linux版本  
服务器或者Linux单板使用命令  
strings -a  libHA.AUDIO.DOLBYPLUS.decode.so  | grep GCC:  
Android单板使用命令：  
busybox strings -a  libHA.AUDIO.DOLBYPLUS.decode.so  | grep GCC:  
识别方法（依据使用的编译器类型）：  
    1、Android版本组件库打印：  
       GCC: (GNU) 4.6.x-google 20120106 (prerelease)  （字符出现google相关编译器信息）  
    2、Linux版本组件库打印：  
       GCC: (Hisilicon_v200(gcc4.4-290+glibc-2.11+eabi+nptl)) 4.4.1  （字符出现Hisilicon相关编译器信息）       

####3716Mv300非高安烧录器寄存器配置：
烧录器需要将179置为0x38，将177置为0x01

####c30 备忘：
1. system_ok=3 打开串口  
2. 现在的版本带上了数字签名校验，CI版本才有数字签名的，如果你自己编译的版本升级不上去，你就设置一下参数upgrade_signature=0\  
3. 删掉log_targets参数，打印出media部分日志  

####3719M-C10 中间件编译：  
ip ： 10.10.2.67  
user: root  
passwd: xws123  
基线android中间件路径： /root/android_middleware/android_middleware_develop/swiptvmw_android  
环境变量：android_c10_base.env  

####3719M-C10 epg设置：
帐号: 887788 密码:1  
统一管理工具可以设置  
或者使用串口命令：  
swgetlog  
addcmd  
sw_parameter_set "home_page" "http://10.10.10.120:33200/EPG/jsp/AuthenticationURL"  
sw_parameter_save

####强制卸载（当设备busy不能卸载）：
busybox fuser -k -m /mnt/sdb/sdb1  
umount  /mnt/sdb/sdb1  

####串口修改misc：
echo "boot-recovery" >/dev/block/platform/hi_mci.1/by-name/misc

####生产测试之Wifi:
iwconfig  
ping -I wlan0 192.168.1.1  
netcfg  
netcfg eth0 down  
iwlist wlan0 scanning  
wpa_cli    ??  
ping -c 6 192.168.1.1  
tcpdump -s0 -i wlan0 &   
tcpdump -s0 -i wlan0 -w /data/aa.cap  &  
busybox route add default gw 192.168.1.1 dev wlan0  

####伪 随机流 脚本：
while :  
do  
monkey -p com.android.launcher -v 5000  
monkey -p com.android.settings -v 1000  
monkey -p net.sunniwell.app.swsettings -v 1000  
done  


####手动启动launcher：
I/ActivityManager( 1308): START {act=android.intent.action.MAIN cat=[android.intent.category.HOME] flg=0x10200000 cmp=net.sunniwell.launcher.swlauncher.huawei/.Launcher} from pid 1308   
 ----手动：am start -n net.sunniwell.launcher.swlauncher.huawei/.Launcher  

####TestVideo.apk  sh下输入URL
input text "file:///mnt/sda/sda1/Videos/6ch_voices_id_7_dd_h264.trp"

####查看堆限制，每个进程可用内存数：
getprop | grep dalvik.vm.heapgrowthlimit   

####Linux 盒子cmdline：
bootargs=mem=230M console=ttyAMA0,115200  root=/dev/romblock8 rootfstype=cramfs ro  mtdparts=hinand:1M(fastboot),1M(partition),6M(boot1),64M(app),1M(logo1),1M(param1),1M(playlist),6M(boot2),64M(app2),1M(logo2),1M(param2),1M(playlist2),108M(swfs),256M@0M(whole) mmz=ddr,0,0x8e600000,282M LogBufSize=0x80000 extra_ref=2 extra_disp=0  
部分代码：  
sprintf(g_cmdline+strlen(g_cmdline), " mmz=ddr,0,0x%x,%dM LogBufSize=0x%x extra_ref=2 extra_disp=0", (m_kernel_size << 20)+0x80000000,(m_chipsize-m_kernel_size), LOGBUFSIZE );  
由此可见：mem=230M为分配给内核控制的内存大小，mmz=ddr,0,0x8e000000,282M 即mmz地址为可用内存起始地址0x80000000 加上分配给内核控制的内存大小0xe600000(230M)最终结果为0x8e600000, mmz的大小则为内存总大小512减去分配给内核的230M最终结果为282M。


####马电flash：
samsung 240  
KLM4G1FE3B-B001

####测试flash读写速度：
time dd if=/dev/zero of=test.dbf bs=8k count=300000

####8D40A/8C40G(3719M CPU)寄存器：
复用寄存器基地址： 0xF8A21000  
gpio7_2复用   0xf8a210cc  1  
gpio5_0复用   0xf8000044  0x00000120  
gpio2_5复用   0xf8a21054  0x1  
gpio 配置地址：  
gpio2 0xf8b22000  
gpio7 0xf8b27000  
gpio5 0xf8004000  
/*************** new ******************/  
gpio16_2复用   
/*************** new ******************/  
TODO: 非高安uImage编译并同步到小系统、ptcl/马电高安bootimg在小系统中做、平台管控

####ptcl 读取loader
setenv ipaddr 172.16.6.117;setenv serverip 172.16.6.115;setenv netmask 255.255.255.0;  
//boot区  
mw.b 0x82000000 0xff 200000  
mmc bootread 0 82000000 0 1000; tftp 82000000 loader_read_no8.bin 200000  
//非高安的话，没有boot区，则需要读写mmcblk0 的前2M数据（跳过512字节MBR）  
mw.b 0x82000000 0xff 200000   
mmc read 0 82000000 0 1000; tftp 0x82000000 loader_fromuser.bin 200000   

####8841C 烧写mac
    ----版本：Scanner-gaoan  
    ----配置：举例如下，关键是位数  
![pic_002](res/Accumulating/accumulating_002.png)  


####3716C 寄存器：
gpio5_0/1/2/3/4/5/7 模式，基地址：0xF8000000  偏移地址: 044, 数值0x00000001(gpio5_0/1)  
复用寄存器基地址：0xF8A21000      
gpio1_5 模式：偏移地址/数值 034/0  
gpio8_4 模式：偏移地址/数值 0F0/1  
gpio17_0 模式：偏移地址/数值 200/0  
gpio 高低电平配置地址：  
gpio1 基地址：0xF8B21000  
gpio5 基地址：0xF8004000  
gpio8 基地址：0xF8B28000  
gpio17 基地址：0xF8B31000  

####制作各种镜像文件：
 550 * 1024 * 1024 = 576716800  
$ANDROID_BUILD_TOP/out/host/linux-x86/bin/make_ext4fs -s -l 576716800 -a user_app ./user_app.img ./user_app  
$ANDROID_BUILD_TOP/out/host/linux-x86/bin/simg2img user_app.img user_app.ics.ext4  
 248 * 1024 * 1024 = 260046848  
$ANDROID_BUILD_TOP/out/host/linux-x86/bin/make_ext4fs -s -l 260046848 -a backup ./backup.img ./backup  
$ANDROID_BUILD_TOP/out/host/linux-x86/bin/simg2img backup.img backup.ics.ext4  

####HDCP:
从不支持HDCP频道切换到支持HDCP的频道，做了两件事：  
a、开启HDCP,设置HDCP属性后由芯片逻辑和电视自行完成认证，软件无法干预；  
b、切换制式；

####马电盒子测试账号mac：
000763e3a13d

####iptv 账号：
10807/1

####马电HyppTV配置：
IPTV Settings :  
    Primary Address:  http://10.10.10.68:33200/EPG/jsp/index.jsp  
Date and Time:  
    NTP server address:  10.10.10.80  
Account:  
    4455/1  
ott Account: 201345/1  



####数据库操作(浙江联通epg地址)：
cd  /data/data/com.android.providers.settings/databases  
sqlite3 settings.db  
select * from secure;  
select * from secure where name='tvos_home_page';  
update secure set value='http://101.69.250.135:8082/EDS/jsp/AuthenticationURL'  where name='tvos_home_page';  

####网卡4mA、8mA鉴别：
himd.l 0x10200068  
himd.l 0x10200070  
两个寄存器都需要查看，如果是0x102则是4mA,0x106为8mA  
单网卡只需关注68寄存器  
3716M上需要配置此网卡能力，3716C不需要  


####浙江移动(添加四个参考帧)：
mkbootimg --kernel uImage --ramdisk ramdisk.img   --cmdline  "mem=307M vmalloc=400M console=ttyAMA0,115200 extra_ref=4 extra_disp=0 lpj=5996758 mtddev=blackbox androidboot.console=ttyAMA0 mmz=ddr,0,0x9D600000,42M mtdparts=hi_emmc:" --base 0x80000000 --output boot.img

####马电融合版本新内存分配（307M）：
//打开串口的bootimg：  
除了需要将cmdline中的console=ttyAMA0,115200  androidboot.console=ttyAMA0,   还需要init.rc中去掉sw_monitor的启动，顺便打开一下adb  
//ramdisk.img  
mkbootfs out/target/product/godbox/root | minigzip > ramdisk.img  
//boot.img-->boot-for-factoryimg.img  
mkbootimg --kernel kernel/arch/arm/boot/uImage --ramdisk ramdisk.img   --cmdline  "mem=307M vmalloc=400M console=NULL  lpj=5996758 mtddev=blackbox androidboot.console=NULL mmz=ddr,0,0x9BA00000,42M mtdparts=hi_emmc:" --base 0x80000000 --output boot.img  
（swbootsign:device/hisilicon/godbox/prebuilt/swboot_sign/swsign -f boot_1.img_test -s device/hisilicon/godbox/prebuilt/swboot_sign/sunniwell_rsa_priv.txt -t kernel -e 0）  
//boot.img-->boot-for-updatezip.img  
mkbootimg --kernel BOOT/uImage --ramdisk ramdisk.img   --cmdline  "mem=307M vmalloc=400M console=NULL  lpj=5996758 mtddev=blackbox androidboot.console=NULL mmz=ddr,0,0x9BA00000,42M mtdparts=hi_emmc:" --base 0x80000000 --output boot_update.img  
（swbootsign:device/hisilicon/godbox/prebuilt/swboot_sign/swsign -f boot_1.img_test -s device/hisilicon/godbox/prebuilt/swboot_sign/sunniwell_rsa_priv.txt -t kernel -e 1）  
//打开串口版本：boot.img-->boot-for-updatezip.img  
mkbootimg --kernel BOOT/uImage --ramdisk ramdisk.img   --cmdline  "mem=307M vmalloc=400M console=ttyAMA0,115200  lpj=5996758 mtddev=blackbox androidboot.console=ttyAMA0 mmz=ddr,0,0x9BA00000,42M mtdparts=hi_emmc:" --base 0x80000000 --output boot_update.img  
（swbootsign:device/hisilicon/godbox/prebuilt/swboot_sign/swsign -f boot_1.img_test -s device/hisilicon/godbox/prebuilt/swboot_sign/sunniwell_rsa_priv.txt -t kernel -e 1）  


####浙江联通8040c（EMMC）制作镜像：
mkbootfs out/target/product/godbox/root | minigzip > ramdisk.img  
mkbootimg  --kernel kernel/arch/arm/boot/uImage --ramdisk ramdisk.img  --cmdline "mem=335M vmalloc=400M console=ttyAMA0,115200 lpj=5996758 mtddev=blackbox androidboot.console=ttyAMA0 mmz=ddr,0,0x9D600000,42M mtdparts=hi_emmc:"  --base 0x80000000 --output boot.img  
///////////////有待验证/////////////////  
mkbootfs out/target/product/godbox/recovery/root/ | minigzip  > ramdisk-recovery.img  
mkbootimg --kernel kernel/arch/arm/boot/uImage --ramdisk ramdisk-recovery.img --cmdline "mem=452  console=ttyAMA0,115200 mmz=ddr,0,0x9c400000,60M mtdparts=hi_emmc:" --base 0x80000000 --output recovery.img  
///////////////有待验证/////////////////  
 

####串口寄存器设置（gpio7_1）：
himm 0x10203130 0x01  
himd.l 0x101ed400  
himm 0x101ed400 0xc2  
himd.l 0x101ed3fc         
himm 0x101ed3fc 0xfd （关）  
himm 0x101ed3fc 0xff （开）  

####stb管理工具：
 huawei/.287aW

####backup 分区：
mount -t ext4 /dev/block/mmcblk0p5  /backup

####PTCL（1G内存）制作镜像：
制作ramdisk  
mkbootfs out/target/product/godbox/root | minigzip > ramdisk.img  
添加播放控制参数：extra_ref=6 extra_disp=0，vdec增大24M，mem相应减小24M   
==== mkbootimg --kernel  kernel/arch/arm/boot/uImage --ramdisk ramdisk.img --cmdline "mem=731M vmalloc=600M  extra_ref=6 extra_disp=0 console=ttyAMA0,115200 lpj=5996758 mtddev=blackbox androidboot.console=ttyAMA0 loglevel=0  mmz=ddr,0,0xBAA00000,42M mtdparts=hi_emmc:" --base 0x80000000 --output boot.img  
====init.godbox.sh对应参数：/system/busybox/sbin/insmod /system/lib/modules/hi_mmz.ko mmz=vdec,0,0xB1B00000,69M:jpeg,0,0xB6000000,12M:ddr,0,0xB6C00000,52M  
添加播放控制参数：extra_ref=12 extra_disp=0，vdec增大48M，mem相应减小48M  
====mkbootimg --kernel kernel/arch/arm/boot/uImage --ramdisk ramdisk.img --cmdline "mem=707M vmalloc=600M  extra_ref=12 extra_disp=0 console=ttyAMA0,115200 lpj=5996758 mtddev=blackbox androidboot.console=ttyAMA0 loglevel=0  mmz=ddr,0,0xB9200000,42M mtdparts=hi_emmc:" --base 0x80000000 --output boot.img  
====init.godbox.sh对应参数：/system/busybox/sbin/insmod /system/lib/modules/hi_mmz.ko mmz=vdec,0,0xB0300000,93M:jpeg,0,0xB6000000,12M:ddr,0,0xB6C00000,52M  
添加loglevel版本mkbootimg:  
====mkbootimg --kernel kernel/arch/arm/boot/uImage --ramdisk ramdisk.img --cmdline "mem=755M vmalloc=600M console=ttyAMA0,115200 lpj=5996758 mtddev=blackbox androidboot.console=ttyAMA0 loglevel=0 mmz=ddr,0,0xBC200000,42M mtdparts=hi_emmc:" --base 0x80000000 --output boot.img  
====init.godbox.sh对应参数：/system/busybox/sbin/insmod /system/lib/modules/hi_mmz.ko mmz=vdec,0,0xB3300000,45M:jpeg,0,0xB6000000,12M:ddr,0,0xB6C00000,52M
编译mkbootimg（ddr增大20M方案）  
mkbootimg --kernel kernel/arch/arm/boot/uImage --ramdisk ramdisk.img --cmdline "mem=755M vmalloc=600M console=ttyAMA0,115200 lpj=5996758 mtddev=blackbox androidboot.console=ttyAMA0 mmz=ddr,0,0xBC200000,42M mtdparts=hi_emmc:" --base 0x80000000 --output boot.img
编译mkbootimg（hisi推荐内存分配方案）  
mkbootimg --kernel kernel/arch/arm/boot/uImage --ramdisk ramdisk.img --cmdline "mem=775M vmalloc=600M console=ttyAMA0,115200 lpj=5996758 mtddev=blackbox androidboot.console=ttyAMA0 mmz=ddr,0,0xBD600000,42M mtdparts=hi_emmc:" --base 0x80000000 --output boot.img

####sdk完整名称：
HiSTBAndroidV400R001C00SPC050B012  
打51补丁：  
HiSTBAndroidV400R001C00SPC051  

####通过apache模拟ott：
修改一个m3u8文件，只带一个切片即所需测试的ts文件。  
启动iptv后 swgetlog  
sw_media_start http://10.10.4.31/ott/dd.m3u8  

####misc.img信息：
udisk_factory_burn  
boot-recovery  
mmc read 0 82000000 3000 1000（2M/除512）  
tftp 82000000 misc_8040c.img 200000（2M/未除512）  

####8840C(EMMC)编译system.img,system.ics.ext4
make_ext4fs -s -l 335544320 -a system out/target/product/godbox/obj/PACKAGING/systemimage_intermediates/system.img out/target/product/godbox/system  
cd out/target/product/godbox; ./mksystemext4  （使用out/target/product/godbox下的system.img）  

####hdmi输出调试工具：
disp getfmt  
disp --help

####LOGE配置：
    #include <cutils/logd.h>
    #include <cutils/logprint.h>
    #define LOG_TAG "wilsonflying_hisisample"
    Android.mk 添加log库的链接:
    LOCAL_SHARED_LIBRARIES += \
                               libcutils \
                               libutils \
                               liblog

####8000E 和 8000A硬件设计相同

####马电8040E 分区及镜像烧写
setenv ipaddr 172.16.6.56;setenv serverip 172.16.6.55;
setenv ipaddr 10.10.4.35;setenv serverip 10.10.4.31
tftp 82000000 *.img

####tftp  方式烧写emmc镜像
下载：tftp 82000000 misc.img  
recovery.img    
mmc write 0 82000000 4000 8000  
boot.img  
mmc write 0 82000000 10000 4000  
misc    
mmc write 0 82000000 3000 1000  
logo  
mmc write 0 82000000 c000 4000  
deviceinfo  
mmc write 0 82000000 2000 1000  

####马电ott-iptv手动编译bootimg（支持串口版本）：
 mkbootimg --kernel device/hisilicon/godbox/prebuilt/Image/uImage --ramdisk ../../tmp/ramdisk.img_rong   --cmdline  "mem=317M vmalloc=400M console=ttyAMA0,115200  lpj=5996758 mtddev=blackbox androidboot.console=ttyAMA0 mmz=ddr,0,0x9C400000,42M mtdparts=hi_emmc:" --base 0x80000000 --output boot.img_317_serial

####马电ott-iptv手动编译bootimg：
mkbootimg --kernel device/hisilicon/godbox/prebuilt/Image/uImage --ramdisk ../../tmp/ramdisk.img_rong   --cmdline  "mem=317M vmalloc=400M console=NULL  lpj=5996758 mtddev=blackbox androidboot.console=NULL mmz=ddr,0,0x9C400000,42M mtdparts=hi_emmc:" --base 0x80000000 --output boot.img_317

####马电ott手动编译bootimg（支持串口版本）：
mkbootimg  --kernel device/hisilicon/godbox/prebuilt/Image/uImage --ramdisk /home/sunhuasheng/tmp/ramdisk.img_rong  --cmdline "mem=335M vmalloc=400M console=ttyAMA0,115200 lpj=5996758 mtddev=blackbox androidboot.console=ttyAMA0 mmz=ddr,0,0x9D600000,42M mtdparts=hi_emmc:"  --base 0x80000000 --output boot.img_335_serial

####马电ott手动编译bootimg：
mkbootimg  --kernel device/hisilicon/godbox/prebuilt/Image/uImage --ramdisk /home/sunhuasheng/tmp/ramdisk.img_rong  --cmdline "mem=335M vmalloc=400M console=NULL lpj=5996758 mtddev=blackbox androidboot.console=NULL mmz=ddr,0,0x9D600000,42M mtdparts=hi_emmc:"  --base 0x80000000 --output boot.img_335

####tftp 下载镜像到pc:
setenv ipaddr 10.10.4.35;setenv serverip 10.10.4.31  
nand read 82000000 1800000 800000  
tftp 82000000 misc_normal.img 800000  
![pic_001](res/Accumulating/accumulating_001.png)  

####浙江联通fastboot中打开日志（去掉loglevel=0）：
setenv bootargs 'mem=335M vmalloc=400M console=ttyAMA0,115200 lpj=5996758 mtddev=blackbox androidboot.console=ttyAMA0 mmz=ddr,0,0x9D600000,42M mtdparts=hinand:8M(fastboot),8M(partition),8M(deviceinfo),8M(misc),16M(recovery),8M(logo),16M(boot),248M(backup),48M(swdb),224M(cache),320M(system),384M(userdata),256M(data_dalvik),1024M(user_app),1520M(sdcard) androidboot.mac=00:07:63:00:00:00 androidboot.standard=1080i_50Hz  androidboot.serialno=0010010900100000A000000000000000'  
nand read 82000000 3800000 800000;bootimg 0x82000000  

####有关logo：
Fastplay.exe打出logo.img后放到小系统 resouce/Images/logo.img  
小系统中main.mk中有INSTALLED_LOGO_TARGET 控制是否打入logo的img  

####有关android内置命令：
sw_cp ; toolbox rm;swch_mod;  

####有关bootimg：
device/hisilicon/godbox/BoardConfig.mk 修改boot.img中的cmdline  
需要把kernel 编译出来了的uImage  放到device/hisilicon/godbox/prebuilt/Image/  
根目录下make bootimage  

####深圳wifi
TP_LINK_1234/0987654321  
TP_LINK_123/075582883008  

####手动制作升级包：  
把打开串口的boot.img 压缩到zip包里  
cd enable_serial; zip -qry  ../enable-serial.zip   ./*   
然后签名：  
java  -jar  signapk.jar  -w  testkey.x509.pem  testkey.pk8  save-stb-serial.zip  save-stb-serial-signed.zip  

####logcat 打印时间：
logcat -v time

####生产测试版本
先烧fastboot、ptable、misc、recovery，然后在开机时候升级其他部分

####调试技巧
addr2line -e out/target/product/godbox/symbols/system/lib/libswca.so 0x2d3c  
I/DEBUG   (  946):          #00  pc 00002d3c  /system/lib/libswca.so (std_ping)  
I/DEBUG   (  946):          #01  pc 0000f36a  /system/bin/swproduct_wifi  

####进入配置页面，STB管理工具
172.16.6.109:8082  
huawei  
28780808  

####shell环境
busybox sh  

####remount
mount -o remount system /system
mount -o remount  /   /
mount -o  remount,ro /  /

####串口启动升级
把升级包命名为update.zip，push 到/cache然后到命令行输入以下命令，再重启盒子就可以了。
mkdir /cache/recovery;echo --update_package=CACHE:update.zip >/cache/recovery/command;echo boot-recovery > /dev/block/mtdblock2;sync;  
 

####bootargs位置：
device/hisilicon/godbox/BoardConfig.mk  

####签名:
 java -jar out/host/linux-x86/framework/signapk.jar -w build/target/product/security/testkey.x509.pem build/target/product/security/testkey.pk8 ~/full_godbox-ota-80102009.zip ~/full_godbox-ota-80102009_sig.zip

####TM/8940/8800项目代码地址：
repo init -u git@svn.xxHi3716C-ics.git/manifest.git -b ics-hisi3716-121219-spc050

####制作uboot.img
/home/guojia/zhejiang/build/linux-x86/bin/mkbootimg --kernel /home/guojia/zhejiang/resouce/boot/uImage --ramdisk /home/guojia/zhejiang/out/Version/ramdisk.img --cmdline  "mem=312M extra_ref=4 extra_disp=0 vmalloc=400M console=ttyAMA0,115200 lpj=5996758 mtddev=blackbox androidboot.console=ttyAMA0 mmz=ddr,0,0x9D600000,42M mtdparts=hinand:" --base 0x80000000 --output /home/guojia/zhejiang/out/Version/boot.img

####HiMediaPlayer.cpp路径
frameworks/base/media/libmediaplayerservice/HiMediaPlayer.cpp

####调节驱动打印级别：
echo 8 > /proc/sys/kernel/printk

####有关启动时间优化的几个点：
1、裁剪bootloader  
   裁剪不必要的driver和运行的代码  
2、裁剪linux kernel，缩小kernel的size  
   采用Image镜像方式load kernel，而不是采用压缩的kernel.  
   把裁剪不必要的driver.  
   把usb、mmc、ext3、ext4、vfat、NTFS等等没有必要优先加载的driver做成module形式.  
3、优化Android启动脚本，  
  把Android init.rc脚本优化，去掉不必要运行脚本命令。  

####bootimg制作：
 ../../../host/linux-x86/bin/mkbootfs root_bk | minigzip >ramdisk.img
../../../host/linux-x86/bin/mkbootimg --kernel uImage --ramdisk ramdisk.img.gz --cmdline  "mem=335M vmalloc=400M console=ttyAMA0,115200 loglevel=0 lpj=5996758 mtddev=blackbox androidboot.console=ttyAMA0 mmz=ddr,0,0x9D600000,42M mtdparts=hi_mmc:" --base 0x80000000 --output boot.img
 
####zygote 入口：
frameworks/base/cmds/app_process$

####bootchart：
$ adb shell 'echo 120 > /data/bootchart-start' $ adb shell 'mkdir /data/bootchart'
java -jar /usr/share/bootchart/bootchart.jar /tmp/android-bootchart/bootchart.tgz

####编译:
source build/envsetup.sh  
lunch 4 2 2  
mmm -B hardware/libhardware_legacy/ -j4  
mmm -B frameworks/base/core/jni/  
打升级包  
make otapackage  

####adb使用
adb kill-server  
adb connect xxx.xxx.xxx.xxx:5555  
adb shell  
adb push 驱动文件 /system/lib/modules/  




###Linux 阶段




####代码可以完全做到不包含头文件，但出于便于阅读还是写好头文件。详见[C语言的头文件是必须的吗？](http://blog.chinaunix.net/uid-24774106-id-3291005.html)

####HMS点播发流方法:
HMS server ip： 172.16.11.90
login_name:  xx
password: xx
1. 找一个可以播放的HMS
2. 既然可以播放，肯定有一个点播卷，
   如/home/hms/data/c/vod_pub_c
3. 将ts文件拷贝到点播卷目录下，
   如/home/hms/data/c/vod_pub_c/stc_nvod.ts
4. 调用HMS抽帧工具，对TS进行抽帧
   cd /home/huawei/mdn2000/es/bin
   ./ts_get_vod_index /home/hms/data/c/vod_pub_c/stc_nvod.ts
5. 创建一个mdn文件，为空即可
   touch /home/hms/data/c/vod_pub_c/stc_nvod.ts.mdn
6. rtsp://hms_ip/stc_nvod.ts

####ttnet：
开机按遥控器蓝色键进入小系统，按数字键1 进行u盘升级  
mkconfig_ttnet.exe 根据项目编译出来的镜像生成config.ini  
在u盘目录下：/upgrade/DN355_pub_1234 放config.ini 及升级包  
高安kernel签名：  
./swsign -f /mnt/hgfs/share/uImage -s sunniwell_rsa_priv.txt -t kernel -e 0 -b 256  

####打开stbmonitor控制开关：
swboot/swboot_nmp.c  
-#ifndef SUPPORT_SECURELINE  
+//#ifndef SUPPORT_SECURELINE  
     sw_nmp_init();  
-#endif  
+//#endif  


####广东v3版本安全红线关闭：
export SUPPORT_SECURELINE=n  
export SUPPORT_SECURERECTIFICATION=n  
删除参数 delpara log_targets  
然后保存下： savepara  
重启就好了  


####8840A镜像
mksquashfs rootfs/ rootfs.img  
setenv bootargs 'mem=306M vmalloc=600M console=ttyMTD,blackbox console=ttyAMA0,115200 androidboot.console=ttyAMA0 hardwareID=8840A pcie0_sel=x1  initrd=0x82500000,0x2800000 mmz=ddr,0,0x93200000,110M root=/dev/ram'  
mmc read 0 0x82000000 18800 2800;mmc read 0 0x82500000 1b000 14000;bootm 0x82000000  
mmc read 0 0x82000000 18800 18000;mmc read 0 0x82500000 1b000 14000;bootm 0x82000000  

####DNS server调试
dig -t NS .   
/usr/sbin/ss -l  
netstat -l -t -n  
host www.baidu.com  
dig baidu.com @localhost  
dig baidu.com @172.16.6.117  
ping 220.181.111.86  

####切片
ping 128.63.2.53
./segmenter64 udp://224.1.1.11:1234 /tmp/ts/ hs hs.m3u8 5 5 5 1
./segmenter64 ./ccir3311.ts /tmp/ts/ hs hs.m3u8 5 5 5 1
mount  -t tmpfs -o size=120m  tmpfs /mnt/tmp  
ffplay http://xxx.xxx.xx.xxx/test/CCTV-1/index.m3u8  

####iptv 串口调试


####bcm 播放及录制调试
igmp://224.1.1.11:1234
./playpump_ip -d 224.1.1.11 -p 1234 -s 188 -v 4 -t 0  
./settop brutus --exec  
killall -9 brutus  
./testrec file:///tmp/udisk1/test1/  

####broadcom cfe设置
write_eeprom 290 6 00 07 63 0C 84 F5  
write_eeprom 290 6 00 07 69 0B 57 24  
write_eeprom 2d5 1 0       boot1  
write_eeprom 2d5 1 1       boot2  
write_eeprom 28a 1 1       app  (1 0 app1) (1 1 app2)  

####挂载调试
mount -o nolock 172.16.6.116:/nfs/visionetics/rootfs/usr/   /usr  
mount -o nolock 172.16.6.116:/nfs/hi3716MV300_vietnam/rootfs/usr/   /usr  
mount -o nolock 172.16.6.116:/nfs/hi3716BesTV_ctc30_base/rootfs/usr/   /usr  
mount -o nolock 172.16.6.116:/nfs/B500_Hi3716MV300_GUANGDONG_ranger30_dolby/rootfs/usr/ /usr  
mount -o nolock 172.16.11.120:/nfs/sunhuasheng/8000A  /tmp  
mount -o nolock 172.16.6.116:/nfs/B500_Hi3716C_BEIJING_unicom/rootfs/usr/ /usr  
mount -o nolock 172.16.6.116:/nfs/ctc30_sichuan_hi3716MV300/rootfs/usr /usr  
mount -o nolock 172.16.6.116:/nfs/hi3716M_ranger/rootfs/usr/ /usr  
mount -o nolock 172.16.6.116:/nfs/hi3716MV300_vietnam/rootfs/usr/ /usr  
mount -o nolock 172.16.6.116:/nfs/3716C_public/ZHEJIANG_ranger/rootfs/usr/ /usr  
mount -o nolock 172.16.6.116:/nfs/B500_Hi3716MV300_GUANGDONG_ranger30/rootfs/usr/ /usr  
mount -o nolock 172.16.6.116:/nfs/hi3716BesTV_ctc30_base/rootfs/usr/ /usr  
mount -o nolock 172.16.6.116:/nfs/hi3716M_TM/rootfs/usr/ /usr  
mount -o nolock 172.16.6.116:/nfs/hi3716MV300_ctc30_base/rootfs/usr/ /usr  
mount -o nolock 172.16.6.116:/nfs/hi3716C_STC/rootfs/usr /usr  
mount -o nolock 172.16.6.116:/nfs/hi3716MV200_ctc30_base/rootfs/usr/ /usr  
mount -o nolock 172.16.6.116:/nfs/flytv/rootfs/usr/ /usr  
mount -o nolock 172.16.11.216:/nfs/b500_guangdong_ranger30/rootfs/usr /usr  
mount -o nolock 172.16.6.116:/nfs/ST_recovery/rootfs/usr/ /usr  
mount -o nolock 172.16.6.116:/nfs/rootfs_full/usr /usr  
mount -o nolock 172.16.6.116:/nfs/stc7405/rootfs/usr /usr  
mount -o nolock 172.16.6.116:/nfs/hi3716C_ctc30_base/rootfs/usr/ /usr  
mount -o nolock 172.16.6.116:/nfs/xinjiang_hd/rootfs/usr/ /usr  
mount -o nolock 172.16.6.116:/nfs/hi3716C/ZHEJIANG/rootfs/usr/ /usr  
mount -o nolock 172.16.6.116:/nfs/hi3716C/ZHEJIANG_ranger/rootfs/usr/ /usr  
mount -o nolock 172.16.6.108:/nfs/hi3716C/ZHEJIANG/rootfs/usr/ /usr  
mount -o nolock 172.16.6.116:/nfs/hi3716c_app/ /usr  
mount -o nolock 172.16.6.116:/nfs/hi3716C_ranger/rootfs/usr /usr  
mount -o nolock 172.16.6.108:/nfs/hi3716C_ranger/rootfs/usr /usr  
mount -o nolock 172.16.6.116:/nfs/7321b/rootfs/usr /usr  
mount -o nolock 172.16.6.116:/nfs/B500_Hi3716M_GUANGDONG/rootfs/usr /usr  
mount -o nolock 172.16.6.116:/nfs/b500_7208_DMX/rootfs/usr /usr  
mount -o nolock 172.16.6.116:/nfs/ptcl_3560e_ranger/rootfs/usr /usr  
mount -o nolock 172.16.6.116:/nfs/bcm7405/rootfs/usr /usr  
mount -o nolock 172.16.6.116:/nfs/hi3716C/ZHEJIANG_ranger/rootfs/usr/ /usr  
mount -o nolock 172.16.6.116:/nfs/hi3716C/ZHEJIANG_ranger/rootfs/usr/local/bin/named/var /var  
mount -o nolock 172.16.6.116:/nfs/hi3716C/ZHEJIANG_ranger/rootfs/usr/local/bin/named/etc/ /etc  



####配置环境变量，调试库文件
export LD_LIBRARY_PATH=':/mnt/hd/lib:/tmp:/tmp/lib:/usr/local/lib:/usr/lib:/lib'  
export LD_LIBRARY_PATH=':/mnt/hd/lib:/tmp:/tmp/lib:/usr/hisi:/usr/lib:/lib'  
export LD_LIBRARY_PATH=':/mnt/hd/lib:/tmp:/usr/flashlite:/usr/lib_dir:/usr/lib:/usr:/lib'  

####STB monitor 常用命令  
172.16.6.117 upgradesoft [file_url]  /f  
172.16.6.117 setpara mac "00:07:56:0C:7F:E8" /s  
172.16.6.117 getpara mac  
172.16.6.117 setpara log_level 0 /s  


####IPTV播放常用命令
sw_log_set_level 0  
sw_browser_show 0    
sw_graphics_print_info   
sw_parameter_set defaultnetmode "static"  
sw_parameter_set("lan_ip","172.16.6.117")  
sw_parameter_set("lan_gateway","172.16.6.1")  
sw_parameter_set("lan_mask","255.255.255.0")  
sw_parameter_set_readonly "mac" 0  
sw_parameter_set("mac","00:07:56:0C:7F:E8")  
sw_parameter_save  
sw_media_play file:///tmp/udisk1/alice_bmp08Mbps_notag.mpg  
sw_media_start rtsp://172.16.11.96/racecar_300.wmv 1 0  
sw_media_play mms://172.16.11.96/racecar_300.wmv   
sw_media_play rtsp://172.16.11.96/racecar_300.wmv  
sw_log_set_level 0  
sw_browser_show 0  
sw_parameter_set_readonly "kernel_dram_size"  0  
sw_parameter_set_int "kernel_dram_size" 84  
sw_parameter_set_int "kernel_dram_size" 458  
sw_parameter_get "kernel_dram_size" ?? 16  
//rtsp  
sw_media_start rtsp://172.16.11.90/88888888/16/20130117/beijing/beijing.ts  
sw_media_start rtsp://172.16.11.90/88888888/16/20130117/h264_aac/test_h264_aac.ts  
export force_vsync=y  
sw_graphics_print_info  
sw_parameter_set_readonly "mac" 0  
sw_browser_open_url file:////usr/local/etc/LocalPlayer/index.html  

####配置ip
ifconfig eth0 172.16.6.117 broadcast 172.16.6.255 netmask 255.255.255.0  
route add default gw 172.16.6.1 dev eth0  
ifconfig eth0 hw ether 00:07:56:0C:7F:E8  


### 有关烧写镜像
#### ip转img:
dd if=sbox_loader_8800_hi3716C_2000011.ip of=sbox_loader_8800_hi3716C_2000011.img bs=1 skip=128  
设置ip：  
setenv ipaddr 172.16.6.117;setenv serverip 172.16.6.115;setenv netmask 255.255.255.0;saveenv  
烧写loader  
mw.b 82000000 ff 100000;tftp 82000000 fastboot-burn.bin;nand erase 0 100000;nand write 82000000 0 100000  
烧写kernel：  
mw.b 82000000 ff 600000;tftp 82000000 uImage;nand erase 200000 600000;nand erase 3b00000 600000;nand write 82000000  200000 600000;nand write 82000000 3b00000 600000  
烧写文件系统(使用cramfs)：  
mw.b 82000000 ff 3000000;tftp 82000000 2.img;nand erase 800000 3000000;nand erase 4100000 3000000;nand write 82000000 800000 3000000;nand write 82000000 4100000 3000000

####配置环境
setenv ipaddr 172.16.6.117               //设置板子IP  
setenv serverip 172.16.6.115               //设置tftp服务器IP  
setenv netmask 255.255.255.0          //设置子网掩码  
setenv ipaddr 172.16.6.117;setenv serverip 172.16.6.115;setenv netmask 255.255.255.0  
setenv bootcmd 'nand read 0x82000000 0x200000 0x300000;bootm 0x82000000'  

####烧写loader
mw.b 82000000 ff 100000                //将82000000开始的1M内存设置为0xff  
tftp 82000000 fastboot-burn.bin      //使用tftp下载loader 文件名为 fastboot-burn.bin  
nand erase 0 100000                     //将起始地址为0的1M flash空间擦除  
nand write 82000000 0 100000           //将82000000内存开始的1M内容 复制到起始地址为0的flash中  
mw.b 82000000 ff 100000;tftp 82000000 fastboot-burn.bin;nand erase 0 100000;nand write 82000000 0 100000  

####下载linux kernel "boot1"：
mw.b 82000000 ff 600000                    //将82000000开始的6M内存设置为0xff  
tftp 82000000 hi_kernel.bin           //下载kernel到ddr  
nand erase 200000 600000                //擦写0x200000后面的6M flash空间  
nand erase 3b00000 600000                //擦写0x3b00000后面的6M flash空间  
nand write 82000000 200000 600000      //把ddr中的kernel写入nand flash，写入6M的大小  
nand write 82000000 3b00000 600000     //把ddr中的kernel写入nand flash，写入6M的大小  
mw.b 82000000 ff 600000     ;tftp 82000000 uImage;nand erase 200000 600000;nand erase 3b00000 600000;nand write 82000000 200000 600000;nand write 82000000 3b00000 600000  
/////////mw.b 82000000 ff 600000     ;tftp 82000000 uImage;nand erase 1000000 400000;nand erase 1400000 400000;nand write 82000000 1000000 400000;nand write 82000000 1400000 400000  

####下载文件系统(使用yaffs)：
mw.b 82000000 ff 3000000               //将ddr写为0xff, 30000000这个值根据文件系统镜像大小可能需要改动，一般应该大于镜像的大小  
tftp 82000000 rootfs.yaffs                //下载rootfs到ddr  
nand erase 800000 3000000                //擦写0x800000后面的0x3000000B flash空间  
nand erase 4100000 3000000                //擦写0x4100000后面的0x3000000B flash空间  
nand write.yaffs 82000000 800000 $(filesize)     //$(filesize)参数不用变动会自动识别tftp下载的文件大小  
nand write.yaffs 82000000 4100000 $(filesize)      //把ddr中的rootfs写入nand  
mw.b 82000000 ff 3000000;tftp 82000000 3.img ;nand erase 800000 3000000 ;nand erase 4100000 3000000;nand write.yaffs 82000000 800000 $(filesize);nand write.yaffs 82000000 4100000 $(filesize);  
需要修改setenv bootargs 'mem=128M console=ttyAMA0,115200  root=/dev/mtdblock8 rootfstype=yaffs rw  mtdparts=hinand:1M(fastboot),1M(partition),6M(boot1),48M(app),1M(logo1),1M(param1),1M(playlist),6M(boot2),48M(app2),1M(logo2),1M(param2),1M(playlist2),12M(swfs),128M@0M(whole) mmz=ddr,0,0x88000000,128M LogBufSize=0x80000'  

####下载文件系统(使用cramfs)：
mw.b 82000000 ff 3000000               //将ddr写为0xff, 3000000这个值根据文件系统镜像大小可能需要改动，一般应该大于镜像的大小  
tftp 82000000 rootfs.cramfs           //下载rootfs到ddr  
nand erase 800000 3000000                //擦写0x800000后面的0x3000000B flash空间  
nand erase 4100000 3000000                //擦写0x4100000后面的0x3000000B flash空间  
nand write 82000000 800000 3000000     //把ddr中的rootfs写入nand flash  
nand write 82000000 4100000 3000000 //把ddr中的rootfs写入nand flash
boot会自动识别$(filesize)为rootfs.yaffs的大小  
mw.b 82000000 ff 3000000;tftp 82000000 2.img;nand erase 800000 3000000;nand erase 4100000 3000000;nand write 82000000 800000 3000000;nand write 82000000 4100000 3000000  
需要修改setenv bootargs 'mem=128M console=ttyAMA0,115200  root=/dev/romblock8 rootfstype=cramfs rw  mtdparts=hinand:1M(fastboot),1M(partition),6M(boot1),48M(app),1M(logo1),1M(param1),1M(playlist),6M(boot2),48M(app2),1M(logo2),1M(param2),1M(playlist2),12M(swfs),128M@0M(whole) mmz=ddr,0,0x88000000,128M LogBufSize=0x80000'  

####下载logo：
mw.b 82000000 ff 100000 //将ddr写为0xff，写1M   
tftp 82000000 logo.img //下载logo到ddr  
nand erase 3800000 100000 //擦写0x3800000后面的1M flash空间  
nand write 82000000 3800000 100000 //把ddr中的partition写入nand flash，写入1M的大小  

####下载partition：
mw.b 82000000 ff 100000 //将ddr写为0xff，写1M  
tftp 82000000 part.img //下载logo到ddr  
nand erase 100000 100000 //擦写0x3800000后面的1M flash空间  
nand write 82000000 100000 100000 //把ddr中的partition写入nand flash，写入1M的大小  
mw.b 82000000 ff 100000;tftp 82000000 partition;nand erase 100000 100000;nand write 82000000 100000 100000;  

其他的分区可以参照现有分区表进行操作  
分区名称     起始地址     分区大小  
"fastboot", 0x00000000, 0x00100000  
"partition",0x00100000,0x00100000  
"boot1", 0x00200000, 0x00600000  
"app", 0x00800000, 0x03000000  
"logo1", 0x03800000, 0x00100000  
"param1", 0x03900000, 0x00100000  
"playlist",0x03A00000, 0x00100000  
"boot2", 0x03B00000, 0x00600000  
"app2", 0x04100000, 0x03000000  
"logo2", 0x07100000, 0x00100000   
"param2", 0x07200000, 0x00100000  
"playlist2",0x07300000,0x00100000  
"swfs", 0x07400000, 0x00900000  
"whole", 0x00000000, 0x08000000  

####nfs 挂载文件系统
setenv bootargs 'mem=96M console=ttyAMA0,115200  root=/dev/nfs rw nfsroot=172.16.6.116:/nfs/hi3716C/ZHEJIANG/rootfs ip=172.16.6.117::172.16.6.1:255.255.255.0 mac=E6:5B:02:C0:60:FF mtdparts=hinand:1M(fastboot),1M(partition),6M(boot1),48M(app),1M(logo1),1M(param1),1M(playlist),6M(boot2),48M(app2),1M(logo2),1M(param2),1M(playlist2),12M(swfs),128M@0M(whole) mmz=ddr,0,0x86000000,160M LogBufSize=0x80000'  
####威科姆盒子
setenv bootargs 'mem=128M console=ttyAMA0,115200  root=/dev/romblock3 rootfstype=cramfs rw  mtdparts=hi_sfc:1M(fastboot);hinand:1M@1M(partition),6M(boot1),48M(app),1M(logo1),1M(param1),1M(playlist),6M(boot2),48M(app2),1M(logo2),1M(param2),1M(playlist2),12M(swfs),128M@0M(whole) mmz=ddr,0,0x88000000,354M LogBufSize=0x80000'


####bcm烧写命令:
配置IP：ifconfig eth0 -addr=172.16.6.117 -mask=255.255.255.0  
查看分区：show devices  
烧写：flash -noheader 172.16.6.115:cfe.bin flash0.loader  
      flash -noheader 172.16.6.115:sbox_boot_7700_97208B0_7133002-463M.img flash0.boot1  
      flash -noheader 172.16.6.115:sbox_app_7700_97208B0.img flash0.app  

####新装的Ubuntu出现vmtools无法安装问题，弹如下内容的错误框：
VMware Tools installation cannot be started manually while Easy Install is in progress.  
如下修改，重启即可  
![pic_005](res/Accumulating/accumulating_005.png)  
