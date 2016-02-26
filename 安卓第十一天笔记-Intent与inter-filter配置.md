# AndroidStudy
Android学习
#Intent与inter-filter配置#
## 1.Intent对象简述 ##
>Android应用中有包含三种重要组件:Activity,Service,BroadcastReceiver(另外还有一个ContextProvider)应用程序采用了一致的方式来启动它们---都是依靠Intent来启动的,Intent就是封装了程序想要启动的程序的意图,不仅如些,Intent还可用于与被启动组件交换信息

Activity:   

         startActivity(Intent intent)
         startActivityForResult(Intent intent,int requestCode)
Service:
        
        ComponentName startService(Intent intent)
        boolean bindServcie(Intent intent,ServiceConnection conn,int flags)

BroadcastReceiver

        sendBroadcast(Intent intent)
        sendBroadcast(Intent intent,String receiverPremission)
        sendOrderedBroadcast(Intent intent,String receiverPremission,BroadcastReceiver resultReceiver,Handler scheduler,int initiaCode,String initialData,Bundle initiaExtras)
        sendOrderBroadcast(Intent intent,String receiverPermission)
        sendStickyBroadcast(Intent intent),
        sendStickyOrderdBroadcast(Intent intent,BroadcastReceiver resultReceiver,Handler scheduler,int initiaCode,String initialData,Bundle initiaExtras)

##2.Intent对象的属性##
>大致包含:Component,Action,Category,Data,Type,Extra,Flag这7种属性,其中Component用于明确指定需要启动的目标组件,而Extra用于携带需要交换的数据
##3.Component属性##
>Intent的Component属性需要接受一个ComponentName对象,ComponentName包含如下构造器

    ComponentName(String pkg,String cls)
    //创建PKG据在包下的cls类所对应的组件
    ComponentName(Context pkg,String cls)
    //创建PKG据在包下的cls类所对应的组件
    ComponentName(Context pkg, Class<?> cls)
    //创建PKG据在包下的cls类所对应的组件

>同时还有如下三个方法

    setClass(Context packageContext,Class<?> cls)
    //设置该 Intent将要启动的组件对应的类
    setClassName(Context packageContext,String className)
    //设置该Intent将要启动的组件对应 的类名,要全路径
    setClassName(String packageContext,String className)
    //设置该Intent将要启动的组件对应 的类名,要全路径
>Context代表访问应用环境信息的接口,而Android应用的包名则作为应用的唯一标识,因此 Android应用的Context对象与该应用的包名有一一对应的关系
Component属性的Intent已经明确了它将要启动哪个组件,因此这种Intent也被称为显示意图
常用来启动同一个应用程序中组件
而没有指定Componet属性的Intent被称为隐式意图,隐式意图没有明克指定去启动哪个组件,就用将会根据Intent指定的规则去启动符合条件的组件,具体是哪个不确定

*使用显示意图启动另一个Activity
    
     /**
     * 显示意图打开第二个Activity
     * @param v
     */
    public void open02(View v){
         //创建一个Compon对象
        ComponentName comp = new ComponentName(this,SecondActivity.class);

        Intent intent = new Intent();
        //为Intent设置Compon属性
        intent.setComponent(comp);
        //启动
        startActivity(intent);
    }
>第二个Activity

    tv = (TextView) findViewById(R.id.tv);
        //得到Intento
        Intent intent = getIntent();

        //获取Componet
        ComponentName comp = intent.getComponent();

        tv.setText("包名" + comp.getPackageName() + ",组件名称:" + comp.getClassName());

>更改启动Intent的写法

    /**
     * 显示意图打开第二个Activity
     * @param v
     */
    public void open02(View v){
    

        Intent intent = new Intent();
        //为Intent设置属性
        intent.setClass(this,SecondActivity.class);
        //启动
        startActivity(intent);
    }
下面这种写法更加简单明了
##4.Action,Category属性与intent-filter##
>Action与Category属性的值都是一个普通的字符串
>其中Action代表该Intent所要完成的动作(抽象)而Category刚用于Action增加额外的附加类别信息.通常Action会与Category属性结合使用

隐式意图启动一个Activity

    <!--注册SecondActivity-->
        <activity android:name=".SecondActivity">
            <intent-filter>
                <!--添加动作名称可以随便写-->
                <action android:name="com.hello.SECOND"/>
                <!--类别如果是这个默认DEFAULT可以不写,但建议写上-->
                <category android:name="android.intent.category.DEFAULT"/>
            </intent-filter>
        </activity>

>启动一个Activity

    

        Intent intent = new Intent();
        //为Intent设置属性
        intent.setAction("com.hello.SECOND");
        intent.addCategory("android.intent.category.DEFAULT");
        //启动
        startActivity(intent);

*其中Action与Category都可以有多个值
    
    //获取Action与Category的值
        String action = intent.getAction();

      Set<String> categories = intent.getCategories();

##5.指定特定的Action与Category调用系统的Activity##

Android系统内部提供了大量的标准的Action与Category常量


*返回桌面

     /*
    返回桌面
     */
    public void goDesk(View v){
        Intent intent = new Intent();
        //写字符串
        intent.setAction("android.intent.action.MAIN");
        //写字符串
        intent.addCategory("android.intent.category.HOME");
        //启动
        startActivity(intent);

    }

*使用系统提供好的

        Intent intent = new Intent();
        intent.setAction(Intent.ACTION_MAIN);
        intent.addCategory(Intent.CATEGORY_HOME);
        startActivity(intent);

##6.Data Type与intent-filter配置##

Data属性常用于向Action属性提供操作的数据.Data属性接收一个URI的对象,一个uri对象通常通过如下字符串的形式
>content://com.android.contacts/contacts/1
>tel://110

*uri字符串满足以下格式
>schema://host:prot/path

content:是schema部分
com.android.contacts是host部分
port是80省略了
/contacts/1是path部分

>Type属性用于指定该Data所指定的URI对应 的MIME类型,这种MIME类型可以是任意任何自定义的MIME类型只要符合ABC/XYZ格式的字符即可

>Data属性与Type属性关系比较微妙,这两个属性相互覆盖



- 如果为Intent先设置Data属性,后设置Type属性,那么Type属性将会覆盖Data属性
- 如果为Intent先设置Type属性,后设置Data属性,那么Data属性将会覆盖Type属性
- 如果希望Intent即有Data属性,也有Type属性则应该调用Intent的setDataAndType()方法

Data属性
    
     <data
                    android:host=""
                    android:mimeType=""
                    android:path=""
                    android:pathPattern=""
                    android:pathPrefix=""
                    android:port=""
                    android:scheme=""
                    android:ssp=""
                    android:sspPattern=""
                    android:sspPrefix=""/>


1. host:声明Intent的Data属性的host部分
2. mimeType:用于声明IntentData属性Type属性
3. path:声明Data属性中path部分
4. pathPattern所有匹配的Data属性的path部分的正则表达式
5. pathPrefix Data属性的path前缀
6. port :Data属性的port部分
7. scheme:Data属性的协议部分,如:http://,tel://,content://


匹配过程:
    
如果组件的Data元素只指定了android:schema属性,那么只要Intent的Data属性部分与schema部分相同就可启动

     <data android:scheme="abcd"/>
    intent.setData(Uri.parse("abcd://"));

如果组件指定了schema,host,属性那么只要Intent的Data属性的schema,host,prot部分与之相同就可启动,可以 没有port属性

    <data android:scheme="abcd"
                      android:host="www.fkjava.com"
                   />
    intent.setData(Uri.parse("abcd://www.fkjava.com"));

如果目标组件的Data子元素,只有port属性,没有host属性,那么port属性不会起作用
如果目标组件的Data子元素只有path属性,没有host属性,那么path属性不会起作用

    <data android:scheme="abcd"
                      android:host="www.fkjava.com"
                    android:port="8888"/>
    intent.setData(Uri.parse("abcd://www.fkjava.com:8888"));

##7.调用系统的应用##

1. 打开指定浏览器的页面
        
          Intent intent = new Intent();
        //为Intent设置属性
        intent.setAction(Intent.ACTION_VIEW);
        //设置data
        intent.setData(Uri.parse("http://www.qq.com"));
        //启动
        startActivity(intent);

2.打电话
    
     Intent intent = new Intent();
        //为Intent设置属性
        intent.setAction(Intent.ACTION_CALL);
        //设置data
        intent.setData(Uri.parse("tel://110"));
        //启动
        startActivity(intent);

3.编辑指定的联系人

     Intent intent = new Intent();
        //为Intent设置属性
        intent.setAction(Intent.ACTION_EDIT);
        //设置data
        intent.setData(Uri.parse("content://com.android.contacts/contacts/1"));
        //启动
        startActivity(intent);

##8.Extra属性##

>Extra属性用于在多个Actiton之间进行数据传递,Intent的Extra属性值应该是一个Bundle对象,Bundle对象就是一个Map对象,使用intent.putExtra(key,value)就可以保存数据 


##9.FLAG属性##
1.  Intent.FLAG_ACTIVITY_CLEAR_TOP用于清除当前Activity stack中的Activity,相同于singleTask android:launchMode="singleTask"
abcd---启动b,就直接销毁B上面的--AB
2.  Intent.FLAG_ACTIVITY_BROUGHT_TO_FRONT如果通过FLAG启动的Activity已经存在下次再次启动时以这种模式直接把stack中的Activity带到前台,有点singleInstance感觉android:launchMode="singleInstance"
3.  Intent.FLAG_ACTIVITY_NEW_TASK相当于stardard启动模式android:launchMode="standard"
4.  Intent.FLAG_ACTIVITY_NO_ANIMATION,不使用过渡动画
5.  Intent.FLAG_ACTIVITY_NO_HISTORY,没有历史记录,启动完成后就再启动其它的Activity时,这种模式的Activity直接销毁,不在;Stack中,点击返回是看不到的
6.  Intent.FLAG_ACTIVITY_REORDER_TO_FRONT,直接带到Stack顶部abcd,这种启用acdb
7.   Intent.FLAG_ACTIVITY_SINGLE_TOP相当于android:launchMode="singleTop"
