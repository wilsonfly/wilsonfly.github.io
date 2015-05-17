



####硬件feature
android是默认设备带有触摸屏的，所以像在机顶盒、电视允许的系统中允许的话，就可以在AndroidManifest中配置不需要触摸屏：<uses-feature android:name="android.hardware.touchscreen" android:required="false">，运行时检测功能可用性可以用接口getPackageManager().hasSystemFeature("android.hardware.telephony")

####framelayout使用场景
framelayout 不需要调整的时候使用，效率比较高

####scrollview
scrollview 把原来layout中LinearLayout之类的字段替换成ScrollView，再将其中的内容用LinearLayout包裹一下即可

res中资源名字不能用数字打头，而且不能大写

####intent.setDataAndType
intent.setdata settype互斥，可以看下代码在设置一个的时候将另外一个置空了，所以需要两个都设置的时候调用intent.setDataAndType

####intent-filter
AndroidManifests.xml中给一个Activity指定的intent-filter可以有多个，intent-filter中的action、category、data也可以有多个。intent中设置的action只能有一个。



####eclipse创建android项目时，预览layout.xml文件时提示： This version of the rendering library is more recent than your version of ADT plug-in. Please update ADT plug-in，导致无法正常预览布局文件。
问题根源：SDK版本过高，ADT版本过低。可以调节预览页面右上角的android versiont to use when rendering layouts in eclipse，选择较低版本的api。如果不想每次手动调节这个东西，一则按照网上攻略选择help—>install new software升级tools(反正我是没有升级成功，翻墙状态下都根本刷不出来)，二则直接删掉较新的sdk，只留下document即可(删掉后出现了appcompat_v7报错的情况，删之，随便新建个工程即可附带重新生成)。

####用默认配置生成的工程会报错：R cannot be resolved to a variable
查看是没有R.java生成的，将appcompat_v7工程打开。将此工程关闭后采用默认配置生成的工程会立即出现报错。


####broadcast Reciever机制运行效率是比较低的，所以不能用于发送高频率或者发数据的场景。

使用bindService时候需要在service类中实现继承Binder的内部类，然后在onBind方法中返回该内部类的对象。否则在绑定service的时候没有onServiceConnected回调。

####startActivity方式打开音频、视频、图片、txt、word、网址、电话、短信、邮箱等等各种文件或组件

     实例：
     Uri uri = Uri.fromFile(new File("/mnt/sdcard/ice.avi")); 
     intent.setDataAndType (uri, "video/*"); 
     this.startActivity(intent);  

一个service只会有一个实例，多次绑定不会调用多次onCreate。service的整个生命周期有系统控制，所以不能通过new的方式来获取service的实例，需要通过bindService的形式获取。在一个activity中调用startService创建了服务，在该activity退出的时候服务不会被销毁；调用bindService创建的服务，在activity退出的时候服务会被销毁。

####Android推荐使用SparseArray代替HashMap，性能方面会有改善。



####LayoutInflater
LayoutInflater这个类的作用类似于findViewById(),  
不同点：   
LayoutInflater是用来找layout下xml布局文件的,而且它会实例化   
findViewById()是找具体xml布局文件下的具体widget控件，比如：Button按钮    

####Debug快捷键
F5 进入方法  
F6 逐行追踪  
F7 跳出方法  
F8 下一个断电或是结束Debug  


####Can't create handler inside thread that has not called Looper.prepare()   
子线程中handler需要跟looper一起使用

####android 中不允许图片资源的文件名中出现大写字母，且不能以数字开头。

####res\xml\oneXmlFile.xml: Invalid file name: must contain only [a-z0-9_.]

####快捷键
command+shift+o导包操作，推荐用这个，不仅可以导入需要的包，还能删掉多余的包  
command+alt+上/下  复制当前行到上面/下面  
alt+上/下   移动当前行到上面/下面  
command+alt+左/右 前一个/后一个编辑的页面  
ctrl+Fn+F11 切换模拟器的横竖屏  

####handler 负责发送消息，Looper负责接收handler发送的消息，并直接把消息回传给handler自己。


####(0202)am启动apk
am start -n [component] ,此处的component类似于代码里边的包名加类名，不过此处类名可以是完整的类名也可以是简写  
am start -n net.sunniwell.app.swsettings/.SWSettingsActivity  
am start -n net.sunniwell.app.swsettings/net.sunniwell.app.swsettings.SWSettingsActivity  


####有关@Override
@Override是伪代码,表示重写(当然不写也可以)，不过写上有如下好处:   
1、可以当注释用,方便阅读  
2、编译器可以给你验证@Override下面的方法名是否是你父类中所有的，如果没有则报错。例如，你如果没写@Override，而你下面的方法名又写错了，这时你的编译器是可以编译通过的，因为编译器以为这个方法是你的子类中自己增加的方法    

####eclipse快捷键
[动画讲解Eclipse常用快捷键](http://www.techug.com/eclipse-shortcut-keys)  

####15. layout 文件不能有大写字母，只能是a-z, 0-9, or _.  

####14. 有关不同类中方法重名
抛异常信息：java.lang.ClassCastException: android.view.ViewGroup$LayoutParams cannot be cast to android.widget.Gallery$LayoutParams
像imgview.setLayoutParams(new Gallery.LayoutParams(200, 150));
与 iv.setLayoutParams(new ImageSwitcher.LayoutParams(ImageSwitcher.LayoutParams.WRAP_CONTENT,ImageSwitcher.LayoutParams.WRAP_CONTENT));
根据报错找到LayoutParams使用位置，加上限定类。默认是属于ViewGroup

像button4.setOnClickListener(new View.OnClickListener() {
与builder.setPositiveButton("确定", new DialogInterface.OnClickListener() {

####13. testGallery  
attrs.xml 内容不能补全，怎么写出来的？

####12. 有关widget，TabHost/TabWidget/FrameLayout需要引用android中id中相应名称tabhost/tabs/tabcontent，要不然找不到tb或者tb.setup 抛空指针异常。
例如tabhost：不能用android:id="@+id/tabhost"，需要android:id="@android:id/tabhost"   
相应的Tabhost tb = (TabHost) findViewById(android.R.id.tabhost);  

####11. imageButton 点击时候会有整个方块颜色闪动，可以将背景设置为透明来解决此问题。

####10. 有关adapter创建时候制定adapter的选择模式是android自带layout中的一系列值，需要在R前加android限定  
ArrayAdapter<String> adapter = new ArrayAdapter<String>(this, android.R.layout.simple_list_item_single_choice, theItem);

####9. 有关快捷键
alt+s+f  文件格式化对齐  
ctrl+1    弹出报错信息，其中比较好用的是强制类型转换Add cast  
ctrl+/    添加注释及取消注释  
ctrl+e   打开的文件列表，切换文件  
ctrl+F11 运行apk  
ctrl+o 列出当前类中的方法列表  
ctrl+m 放大缩小当前窗口  
ctrl+PageDown/PageUp 切换选项卡，即切换不同文件  
crtl+shift+t 查找当前工程中的方法  
crtl+shift+r 查找当前工程中的文件  
ctrl+shift+o 导入包  

####8. 像RadioGroup/checkbox这样有相同名字listener方法的组件，在import方法的时候会冲突，这个时候在new listenser接口的时候在前面加上包名限定，如：  
          sex.setOnCheckedChangeListener(new RadioGroup.OnCheckedChangeListener() {



####7. @+id 新建一个资源id到R.java中，@id 引用一个资源id。如果引用一个在当前代码之后定义的资源的id，可以先在此新建。后续再@+id也没有影响。
![app_004](res/Accumulating_App/app_004.png)

####6. 创建工程时候指定的包名体现在androidManifest.xml中，在src中新建包名需要一致，否则发现AndroidManifest及gen下包名均是建工程时候起的包名。  
可以在AndroidManifest中更改，保存后会在gen下更新，如果出现多个包共存可以project--clean一下。  

####5. 不能在内部注释
![app_003](res/Accumulating_App/app_003.png)

####4. 填充接口中参数，极有可能包含在某个类中，具体哪个类，可以将鼠标停留在接口上然后从如下提示信息中查看：
![app_002](res/Accumulating_App/app_002.png)

####3. res-drawable 中图片名字不能数字打头  

####2. Didn't find class "*****Activity" on path: /data/app/*******.apk  
--更改包名后可以看到 *.java中package 引用报名自动改变了，下面引用的R路径没有改变，另外AndroidManifest.xml 中package 包名没有改变。这都需要手动改正过来。


####1. 运行时候弹框Your project contains error(s)，please fix them before running your application.  
具体报错：A resource exists with a different case  
--如下，报名包含了跟目录名相同的字段：BitmapShader1，修改包名后ok   
![app_001](res/Accumulating_App/app_001.png)

####0. 运行eclipse报错Fail to create the java Virtual Machine
见[解决Fail to create the java Virtual Machine_百度经验](http://jingyan.baidu.com/article/afd8f4de466baf34e286e917.html)

####adt/sdk/sudio下载地址
详情见[Android开发环境的官网下载地址](http://www.cnblogs.com/tc310/p/3938353.html)  

####linux下安装sdk
详情见[RedHat Linux下安装jdk-6u45-linux-x64.bin](http://www.linuxidc.com/Linux/2014-01/95202.htm)   

####WIN7下jdk安装及环境配置
[JDK安装与环境变量配置](http://jingyan.baidu.com/article/6dad5075d1dc40a123e36ea3.html)  

####eclipse sdk Manager更新问题  
由于墙的原因，基本就是无法更新，可以设置一下http代理，在Android sdk manager-->settings中配置http proxy:http://mirrors.neusoft.edu.cn 端口：80,并且选中【Force https://... sources to be fetched using http://...】  
更多详情见：[AndroidDevTools简介](http://www.androiddevtools.cn/)

