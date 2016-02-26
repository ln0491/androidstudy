# AndroidStudey
Android学习
#Fragment(片段)#
##一.Fragment简介##
*Fragment是3.0引入的API,主要为了解决平板,大屏幕手机显示问题

*Fragment代表了Activity的子模块,因此可以把fragment理解成Activity的片段

*Fragment必须被嵌入Activity中使用
##二.创建Fragment的步骤 ##
1. 创建一个类继承Fragment类或者其子类
2. 重写Fragment中的一些onxxx方法
3. 一般有三个方法要重写


     .onCreate()系统创建Fragment的对象后回调该方法,初始化操作
     .onCreateView()当fragment绘制界面组件时会回调该方法
     .onPause()当用户离开该Fragment时将回调这个方法
##三使用fragment使用Activity##
*创建一个布局用来填充fragment

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              android:orientation="vertical">
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="声音大小"/>
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="速度"/>
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="3D"/>
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="左声道"/>
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="右声道"/>

</LinearLayout>
*创建一个fragement
    public class SoundFragment extends Fragment {


    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {

        View view=inflater.inflate(R.layout.sound_item,null) ;

        return view;
    }

*主界面布局
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <!--容器-->
    <FrameLayout
    android:id="@+id/fl_container"
    android:layout_width="match_parent"
    android:layout_height="0dp"
    android:layout_weight="3">

    </FrameLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <Button
            android:onClick="sound"
            android:layout_width="0dp"
            android:layout_weight="1"
            android:layout_height="wrap_content"
            android:text="声音"/>
    </LinearLayout>

    </LinearLayout>
*Activity
        
          /**
         * 点击声音按键显示fragment
            * @param v
            */
         public void sound(View v){
        //声明Fragment
        SoundFragment soundFragment = new SoundFragment();
        //获取Fragment管理器
        FragmentManager manager = getFragmentManager();
        //开启事务
        FragmentTransaction transaction = manager.beginTransaction();
        //替换掉当前布局中的帧布局容器
        transaction.replace(R.id.fl_container,soundFragment);
        //提交事务
        transaction.commit();
    }

##四生命周期##


- onAttach---用于与Acivity建立连接与断开,当该Fragment被添加到Activity时被回调,只会被调用一次
- onCreate---创建Fragment时被调用
- onCreateView--每次创建Fragment时都会被调用
- onActivityCreated:当Fragment所在的Activity被启动完成后调用
- onStart:启动Fragment时调用
- onResume:恢复Fragment时被回调,在onStart方法后一定会被调用
- onPause:暂停Fragment时被回调
- onStop:停止Fragment时被回调
- onDestroyView:建立与销毁视图
- onDesctory:用于初始化与销毁与Activity中的一样
- onDetach:将这个Fragment从Activity中删除,替换完成时调用这个方法,在onDestroy方法后一定回调用这个方法

##五 Activity与Fragment生命周期对比##

    Activity--------------------Fragment
                                onAttach()
                                onCreate()
                                onCreateView()
    onCreate()                  onActivityCreated
    
     
    onStart()                   onStart()

    onResume()                 onResume()

    onPause()                  onPause()

    onStop()                   onStop()

                               onDestoryView()
                               onDestory()
    onDestory()                onDetach()
