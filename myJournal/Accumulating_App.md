




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
