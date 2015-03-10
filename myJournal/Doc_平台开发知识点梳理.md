
目录
文件修改记录	2
第1章 代码管理	1
1.1 代码仓库及分支	1
1.1.1 大系统部分	1
1.1.2 小系统部分	1
1.2 编译及CI	2
第2章 fastboot	3
2.1 环境变量	3
2.2 相关代码	3
2.3分区变动	3
第3章 recovery	5
3.1 基本功能	5
3.2 有关升级过程中异常	5
3.3 有关更换背景图片	5
3.4	有关recovery的升级	5
第4章 定制功能	6
4.1 HDMI自适应	6
4.2 杜比透传	6
4.3 内置sdcard分区	7
第5章 新添加代码模块	8
5.1 swRootService	8
5.2 swdevinfo	8
5.3 swrecovery_flag	8
5.4 swmakemmcimg	8
第6章 念念碎	9
6.1 有关签名	9
6.2 有关iptv	9
6.3 有关功放	9
6.4 有关产测apk	9
6.5 有关小系统build	9
6.6 有关安全红线之串口	9
6.7 有关给hisi提问题单	10
6.8 有关build.prop的生成	10
6.9 有关小系统修改文件的维护	10
6.10 deviceinfo待修复bug	10


 
第1章 代码管理
1.1 代码仓库及分支
1.1.1 大系统部分
以3798 sdk代码的仓库地址为例：
ssh://gerrit.sys.sunniwell.net/android-hisilicon/hisi3798-kk-20140818/manifest.git
其中android-hisilicon 为平台（linux/android）类型+芯片商，hisi3798-kk-20140818为芯片类型+android版本缩写+sdk获取时间。更详细的命名规则说明见《朝歌Android大系统代码配置管理指导书》。
	分支分为base及develop，芯片商提供的sdk及大补丁上传在base分支维护，朝歌开发代码在develop分支维护，在base上打了补丁后merge到develop分支上。
	device/sunniwell, system/network-modules/dhcpcd, system/network-modules/ppp这三个仓库在原生sdk中是没有的，第一个仓库目前维护rootService代码和releasenote、基线特性列表等文档，后面两个仓库为朝歌定制的网络部分代码。
大系统具体使用规则见文档：《朝歌Android大系统代码配置管理指导书》。
1.1.2 小系统部分
	以3798 小系统为例：
ssh://git@svn.sys.sunniwell.net/android-projects.git/hisilicon/hisi-3798M-spc033-emmc-kk-manifest.git 其中hisilicon为芯片商，hisi-3798M-spc033-emmc-kk-manifest.git为芯片商缩写+芯片类型+当前sdk补丁号+当前版本适用flash类型+android版本缩写+具体仓库名称。
	小系统基本结构如下，其中有六个仓库见加粗部分。
├── apk
├── iptvmw
├── Makefile
└── platform
    ├── build
    ├── on-project
    │   ├── build
    │   ├── pub
    │   │   ├── cache
    │   │   ├── image
    │   │   ├── recovery
    │   │   ├── swproduct
    │   │   ├── system
    │   │   └── system_user
    │   └── src
    ├── release
    │   ├── boot
    │   ├── docs
    │   ├── recovery
    │   ├── release-manifest.xml
    │   └── system
    └── release_user
        ├── boot
        ├── docs
        ├── recovery
        ├── release-manifest.xml
        └── system
	分支命名如8M330-EMMC-BASE/ 8G40C-EMMC-SICHUAN，为硬件版本号+flash类型+项目名称。分支名字这块需要留意，由于创建分支的会有平台开发人员及项目开发人员，容易出现混乱。
小系统具体使用规则见文档：《朝歌Android小系统代码配置管理指导书》。
1.2 编译及CI
	hisi sdk在顶层目录会有两个编译说明文件install_notes_cn.txt、install_notes_en.txt及Makefile，上传sdk时候放到build仓库中维护。为方便大家编译sdk，需要在顶层目录下提供编译脚本，其中包括默认编译整个sdk功能，以及编译某个单独模块的功能或者说明。
	大系统需要搭建两个ci，编译eng和user两种版本出来，编译结果分别上传至小系统的platform/release，platform/release_user仓库中。



 
第2章 fastboot
2.1 环境变量
	同一套sdk需要适配多个不同硬件，牵涉ddr、flash、led、寄存器的fastboot就需要维护多个版本，则采用了环境变量的方式来管理多个版本，环境变量统一在build/swfastbootenv/维护。
	目前的环境变量中涉及硬件型号、flash大小、reg文件名称、是否禁用ctrl+c功能的开关SW_CLOSE_LOADER_KEYED、是否强制打开串口的开关：SW_FORCE_OPEN_CONSOLE。
其中硬件型号用于区分LED及寄存器的配置。
flash大小用于控制不同的分区方案。
reg文件名称用于指定编译fastboot需要的寄存器配置文件：reg，此reg文件原本是在device/hisilicon/Hi3798MV100/BoardConfig.mk中指定，由于reg文件需要用环境变量控制，则此Boardconfig.mk中需要去掉reg的指定。
SW_CLOSE_LOADER_KEYED 控制是否禁用ctrl+c进入fastboot功能。
SW_FORCE_OPEN_CONSOLE 控制是否强制打开串口、adb、adb的root权限，打开开关后会配置androidboot.force_open_console=open传给kernel，init从cmdline读到此开关打开的话则对相应的属性做具体配置。
2.2 相关代码
Fastboot定制代码比较独立，大部分几种在此目录下：
device/hisilicon/bigfish/sdk/source/boot/product/android/loader，由customer_android.c中的Android_Main引用。
	由于分区信息已经由loader/swptable.c决定，在boot/product/main.c 中调用的Android_Main需要向前移动至少到Logo_Main之前。
	customer_android.c 中除了引用loader目录下的代码之外还有一个比较重要的操作就是配置led及寄存器。
2.3分区变动
	分区变动需要做的操作大致如下：
1. swptable.c 中填写分区信息，在partition分区没有数据或者数据异常导致校验不过的时候会将默认分区信息回写到partition分区中。由于存在partition的回写功能，开始调试时候可能出现更换了fastboot但没有按新分区信息设置bootargs，那就需要手动破坏一下partition。比如partition是这样的话“1         partition    0x000200000     0x000200000”，开机上电ctrl+c进入fastboot，执行 mmc write 0 1ffffc0 1000 1000 来破坏partition分区。
    代码：device/hisilicon/bigfish/sdk/source/boot/product/android
    2. BoardConfig.mk，这里有编译sdk时候打镜像的大小，目前我们有了小系统这一机制，版本的镜像都是用小系统打出来的，此文件可以不修改。
    代码路径：device/hisilicon/Hi3798MV100，留意330的文件是BoardConfig_8M330.mk
    3. Hi3798MV100-emmc.xml ，分区表文件，一套sdk可能在不同项目上分区信息不一样，这个文件可以不修改，但需要上传小系统platform/on-project/pub/image/
    代码路径：device/hisilicon/Hi3798MV100/prebuilts
    4. recovery.emmc.fstab ，这里有分区名称及顺序，不像android4.0一样还有分区大小，如果只改动了分区的大小，此文件可以不修改。
    代码路径：bootable/recovery/etc
    5. 小系统中partinfo.conf，编译各个分区的镜像及生产镜像时候都是从这个文件里边读取分区大小。
    代码路径：platform/on-project/pub/swproduct/partinfo.conf
    6. fastboot需要编译三个出来：
        1) 正常编译出来的fastboot.bin 对应小系统platform/on-project/pub/image/loader.bin
        2) 将build/swfastbootenv/fastboot-8M330-D2048-E4096.env中SW_FORCE_OPEN_CONSOLE 打开（记得重新source环境变量），编译fastboot，此fastboot可以强制打开串口、将adb连接的用户设置为root，此fastboot用于在user版本上开发调试使用，对应platform/on-project/pub/image/loader_ForceOpenConsole.bin
        3) 关掉SW_FORCE_OPEN_CONSOLE 开关，打开SW_CLOSE_LOADER_KEYED开关（记得重新source环境变量），编译一个禁掉ctrl+c功能的fastboot.bin 用于user版本，对应platform/on-project/pub/image/loader_user.bin
    7. 验证工作
        1) Hitool烧写小系统中编译出来的镜像，看各个分区是否都挂载正常
        2) 升级小系统编译出来的升级包，看升级是否正常
        3) 用3798_update_product_sign.zip 升级生产镜像，看升级完后能否正常启机，看各个分区是否挂载正常，是否出现要求产测的蓝色界面。



 
第3章 recovery
3.1 基本功能
	1. 遥控器按键可进入recovery 
	2. 响应遥控器按键
3. 实现朝歌定制的6个选择项的功能
	4. 需要支持FAT32/NTFS/EXT文件系统的u盘，以及多分区u盘
	5. 在Android系统中写入标记进入recovery根据写入的标记自动执行升级、清除数据等操作。
6. 由Android系统进入recovery进行的恢复出厂设置需要支持定点清理，包括格式化cache分区、格式化data分区、删除/data/data及/data/dalvik-cache/目录下的内容、格式化swdb、删除内置sdcard中内容
3.2 有关升级过程中异常
	由Android系统中进入recovery进行的升级、清除数据以及手动进入recovery选择的backup升级在中途断电再次上电需要保证重新执行上次未完成的动作。
	由手动按遥控器进入recovery选择的外部升级，在升级过程中断电后再次上电需要保证进入recovery首页。由于手动进入recovery本身属于开发调试操作、客服操作，另外不能保证下次上电u盘还插着、上次使用的同一个升级包仍在原位置，鉴于这些原因只保证上电时候能进入recovery。再次上电进入了recovery如果没有去做升级操作，而是成功执行了其他诸如factoryRest之类的操作，misc中的标记则会被擦掉，下次上电则尝试进入Android，此时由于上次的不完整升级会导致启动失败。
	install_package中存在很多返回点，如果出现某种异常导致升级过程中断，则不擦除标记重启，类似上述提到的中途断电情况。有种情况例外，如果升级包校验失败，则认为没有意义的升级，不再尝试升级此包，擦掉标记重启。
3.3 有关更换背景图片
	Recovery界面除了背景图层显示均为图片组成，包括背景、升级机器人、提示文字、进度条均可替换，并能调整其位置。
	在m321上即实现了一套新界面，如果需要更新可参考。
	在设计图片的时候发现一个有过渡阶的图片会在过渡位置出现光晕，最终都是使用的纯色图片。
3.4	有关recovery的升级
	Recovery的升级指的是整个recovery分区的更新，目前有两种方式：
1. 类似fastboot、logo的更新，制作recovery.img在升级时候写入recovery分区 
2. Android原生方式：在制作升级包的时候根据recovery.img与boot.img的差异制作出差分文件recovery-from-boot.p及脚本install-recovery.sh。开机进入Android系统时候会执行此脚本对recovery分区做校验，如果校验值跟脚本中不一致则会根据boot分区及recovery-from-boot.p制作出recovery数据并更新到recovery分区。
	更多说明见文档《android_4_0基于Hi3716MV300升级recovery参考文档》
 
第4章 定制功能
4.1 HDMI自适应
	hdmi的自适应指的是盒子根据电视（也可能是其他设备如采集卡、功放等）的edid信息，获知电视对制式支持的能力，然后按照需要选择最高制式或者电视最优制式进行配置。
	在hisi支持的制式枚举列表中添加DISPLAY_FMT_AUTO=0xFF；作为自适应参数，此值取值为枚举列表中一个空闲值而不添加在枚举列表的最后是防止hisi在此枚举列表末端添加新支持的高清制式而导致hidisplaymanager中定义的值也得相应的变动。
	讨论出来的自适应相关的规格大致如下：1. hdmi插入到电视上时候如果检测目前需要自适应则需要立即自适应到相应的制式上；2.设置页面中的制式列表中有自适应一项，选中此项时候需要立即自适应到相应的制式上；3.自适应后重启能够自fastboot阶段开始保持之前的自适应结果；4.目前基线及项目上自适应时候选择最优制式，之前做过的电信项目要求的是自适应到最高制式。
	相关代码：
device/hisilicon/bigfish/frameworks/hidisplaymanager/
frameworks/base/core/java/android/os/display/DisplayManager.java
4.2 杜比透传
	这里讲的杜比其实笼统的包括了杜比音频格式ac3/ac3plus以及dts音频格式，由于这些音频格式的数据含有多个声道，将数据交由专门的功放设备完成所有声道音效的解码及声音输出可以比盒子将其解码成两声道声音输出达到更好的效果。
	目前hisi的UNF层解码代码已经封装在了libplayer.so中，解码器的解码PCM、透传THRU、既解码又透传SIMUL已经控制不到。好在hisi平台目前已经做到可以不关心解码模式，只需要控制输出通道hdmi/spdif的输出模式即可达到对输出效果的控制。
	在prepare阶段解析出码流音频格式后即根据属性开关对hdmi/spdif的输出模式进行配置，代码：
device/hisilicon/bigfish/frameworks/himediaplayer/hal/HiMediaPlayer.cpp。
	控制开关persist.sys.audio.audiorender的取值规则如下：
persist.sys.audio.audiorender 取值规则 [7654]为0--HDMI的AUTO模式
HDMI	SPDIF
7	6	5	4	3	2	1	0
-	NO-OUTPUT	PASSTHROUGH	PCM	-	NO-OUTPUT	PASSTHROUGH	PCM
	在3716芯片时代存在约束条件：在HDMI透传时会占用到SPDIF数据通道，所以在HDMI透传时SPDIF只能透传或无输出。在3719及之后的3798已经没有此约束，因此hdmi和spdif可以单独控制。上述属性的取值[7654]与[3210]则可以分别赋值。
	针对上述属性不同赋值的预期结果见文档：《朝歌内部文件--杜比预期结果.xlsx》。
4.3 内置sdcard分区
	目前分区已经将dalvik-cache、user-app、sdcard都合并到userdata分区中，前两者分别以/data/dalvik-cache、/data/app/两目录，sdcard则采用了fuse技术添加挂载点/mnt/shell/emulated，创建/sdcard软连接指向上述挂载点目录，而实质内容保存在/data/media/0目录下。
	相关代码：
system/vold/Volume.cpp
system/core/sdcard


 
第5章 新添加代码模块
5.1 swRootService
	有关gpio、寄存器相关的操作以及其他一些需要root权限的操作统一放在swRootService的后台服务中。
	有关swRootService的设计参加文档《swRootService简介》。
	有关寄存器、gpio、led灯的配置参加文档《boardXML配置说明》。
	相关代码：
device/sunniwell/swrootservice/
5.2 swdevinfo
	序列号、mac、制式、串口开关、hdcpkey保存在deviceinfo分区。这里的数据结构跟fastboot中的回写结构需要保持一致，否则会出现校验不过每次开机重新回写默认值。
	在libsw_devinfo_access.so中提供了接口来设置deviceinfo分区中的参数，另外提供了swdevinfo可执行文件，可在命令行修改参数。
	如果有其他需要fastboot阶段使用的参数，例如m321盒子上cvbs与spdif共用接口具体出于哪个输出模式，也可以酌情添加再devinfo中。
	相关代码：
system/core/libsw_deviceinfo_access/
5.3 swrecovery_flag
串口中执行swrecovery_flag写标记到misc分区中，重启后即可进入recovery。目前可以写入标记使得重启后进入recovery、重启后进入recovery尝试升级、重启后进入recovery进行恢复出厂设置。命令行中执行swrecovery_flag后课间使用说明。
	相关代码：
system/core/libswrecovery_flag/
5.4 swmakemmcimg
	制作生产镜像的工具，放在小系统中：
platform/build/tool/linux-x86/bin/swmakemmcimg
	相关代码：
system/core/mk_emmc_product_img/


 
第6章 念念碎
6.1 有关签名
	新来一套sdk时候，需要将build/target/product/security中签名工具及bootable/recovery/res/key统一换成朝歌文件，否则会出现升级校验失败情况。
6.2 有关iptv
	iptv涉及中间件库及linux基线代码中提供的一系列库，编译的时候需要结合Android的编译环境及linux基线中的代码。
	更多详情见swiptvmw-android代码中的README
6.3 有关功放
	北京白色小功放可以测试方口spdif，使用时候不需要配置即插即用。
	北京黑色大功放方口spdif不能使用，可以测试hdmi及圆口spdif，需要在前面板选择具体输入源。
6.4 有关产测apk
	platform/on-project/pub/swproduct/apk_udisk/ 预留了产测apk的上传路径，产测apk需要督促以上传仓库的形式传递，避免后续查找文件、查找版本号困难。
6.5 有关小系统build
	计划每个平台只使用一个build仓库，除了像更新签名这样的极个别情况，不允许新建分支。这样就须要对build的修改非常慎重，原则上需要平台开发人员负责build仓库的维护，避免项目开发人员由于对此仓库缺少全局的认识等原因做出影响其他项目的改动。
	有新需要尽可能的通过在platform/on-project/build/build_id.mk中添加开关的方式来控制，一来对platform/build仓库的改动更可控，二来方便项目上使用。
	制作升级脚本牵涉到几个python脚本，有时间提早对python了解一下。
6.6 有关安全红线之串口
	很多项目都会有安全方面的要求，普遍要求关串口。
	有关关串口又出现了两种情况：1. 不能有输出 2. 可以有输出但不能有输入
1.	对于关闭输出，我们在通过读取deviceinfo中参数secureline来修改
fastboot的bootargs来控制的。详情见代码：
device/hisilicon/bigfish/sdk/source/boot/product/android/loader/swdevinfo.c
device/hisilicon/bigfish/sdk/source/boot/product/android/loader/swboot.c
system/core/libsw_deviceinfo_access/
		而deviceinfo中secureline的参数是swRootService监测属性persist.sys.secureline的值来做相应配置的。
2.	对于关闭输入，我们是在swRootService中监测属性persist.sys.sw.console的值来做相应配置的。接收输入的其实是个shell，init.rc中可以看到启动/system/bin/sh 作为了一个后台服务console，停掉该服务则不能输入了。而kernel和驱动的一些打印仍然能达到串口上来。
我们推荐使用第2种控制方法，因为这种方法不仅可以立即生效不需要重启，还能看到kernel panic和待机的信息，对开发调试和问题定位也有一些帮助。
6.7 有关给hisi提问题单
对于怀疑是hisi sdk问题的问题，最好先找hisi原生版本做对比验证，发现hisi原生版本也有同样问题的话则可以直接提单。如果原生版本没有条件复现问题或者复现不了问题，在做了进一步分析后仍怀疑是hisi问题的，也可以提单。
至于问题单的填写请见文档《海思问题单及补丁管理说明》，里边有非常详细的描述。
需要提一下的是sdk版本号，可以通过查看属性gsm.version.baseband获取，也可以在device/hisilicon/仓库或者patch仓库中查看提交记录，hisi发布的大补丁会修改此版本号。
6.8 有关build.prop的生成
之前的小系统都是采用build.sh直接生成/system/build.prop，这种方式有个比较大的缺陷就是如果在大系统中有属性的修改，发布版本的时候不能及时发现并修改build.sh，容易出问题。
后来引入了新的脚本build_prop.sh来生成/system/build.prop以及recovery的属性文件：RECOVERY/RAMDISK/default.prop，此脚本是以platform/release/system/build.prop、plaform/release/recovery/root/default.prop为基础，针对需要修改的属性则在原有文件基础上修改，针对需要添加的属性则在原有文件后面进行添加。这样就不需要关心大系统属性的变动，只关心需要修改的属性。
新项目强烈建议去掉platform/on-project/build中的build.sh 和buildinfo_recovery.sh，换用build_prop.sh。
6.9 有关小系统修改文件的维护
	小系统中文件由修改人员负责上传，不要让项目人员代传或者让项目人员从一个项目小系统合入到另外一个项目小系统！
	问题修改人员对修改内容最熟悉，是否有影响、小系统中原有代码是否合入、此次修改的所有文件及代码是否都上传等等情况需要修改人员在实际上传操作中做出判断。
6.10 deviceinfo待修复bug
static int sw_devinfo_check( devinfo_t *pdevinfo )
{
    printf("[%s,%d] start...\n", __FUNCTION__, __LINE__);
    uint16_t checksum = 0;
    if( memcmp( pdevinfo->magic, DEVINFO_MAGIC, DEVINFO_MAGIC_SIZE ) )
    {
        printf( "Error: check devinfo maigic failed \n");
        return -1;
    }
    if( pdevinfo->count > DEVINFO_COUNT_MAX )
    {
        printf("Error: Bad devinfo count \n" );
        return -1;
    }
    checksum = sw_crc16( (unsigned char *)&pdevinfo->count, sizeof( devinfo_entry_t ) * pdevinfo->count + sizeof(uint32_t) + HDCP_KEY_MAX_SIZE );
    if( checksum != pdevinfo->checksum )
    {
        printf( "Error: checksum failed \n" );
        return -1;
    }
    printf("[%s,%d] ok,checksum:0x%x\n", __FUNCTION__, __LINE__, checksum);

    return 0;
}
	上述代码存在bug，加粗部分计算需要进行校验的数据大小时候，结构体中间部分数据应该计算其分配空间大小而不应该是实际数据大小。应该修改为如下：
checksum = sw_crc16( (unsigned char *)&pdevinfo->count, sizeof( devinfo_entry_t ) * DEVINFO_COUNT_MAX + sizeof(uint32_t) + HDCP_KEY_MAX_SIZE );
	由于此代码牵涉校验和回写，在现有项目上修改会导致校验失败而回写deviceinfo分区，从而丢失参数。所以需要在新sdk中修复此bug。
