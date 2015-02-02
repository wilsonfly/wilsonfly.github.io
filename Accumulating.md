




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
sw_browser_open_url file:////usr/local/etc/LocalPlayer/index.html  
sw_parameter_set_readonly "kernel_dram_size"  0  
sw_parameter_set_int "kernel_dram_size" 84  
sw_parameter_set_int "kernel_dram_size" 458  
sw_parameter_get "kernel_dram_size" ?? 16  
//rtsp  
sw_media_start rtsp://172.16.11.90/88888888/16/20130117/beijing/beijing.ts  
sw_media_start rtsp://172.16.11.90/88888888/16/20130117/h264_aac/test_h264_aac.ts  


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
