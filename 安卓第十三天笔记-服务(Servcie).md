# AndroidStudy
Android学习
#Servcie服务
##1.服务概念
> 服务

* windows

> 服务没有界面，一直运行在后台， 运行在独立的一个进程里面


* android

> 服务没有界面，一直运行在后台，默认是运行当前的应用程序进程里面。

##2.建立服务
* 建立一个类继承Service类

        public class ServiceDemo extends Service {
* 在清单文件中注册service
    
        <service android:name="com.ithiema.servicequick.servcie.ServiceDemo"></service>

##3.生命周期
>只会在开启服务时初始化一次

    	@Override
	public void onCreate() {
		//只会在开启服务时初始化一次
		super.onCreate();
	}
>每次开启服务都会执行调用

    @Override
	public int onStartCommand(Intent intent, int flags, int startId) {
		//每次开启服务都会执行调用
		return super.onStartCommand(intent, flags, startId);
	}

>停止服务时,只执行一次

    public void onDestroy() {
		//停止服务时,只执行一次
		super.onDestroy();
	}

* 完整生命周期

> onCreate -- onStartCommand--onDestroy

* 启动多次服务

> onCreate方法只会执行一次， 但是onStartCommand执行多次

* 多次停止服务

> 只会执行一次onDestroy方法。

##4.进程

Foreground process

> 前台进程： 当前与用户进行交互的应用所处的进程

* Visible process

> 可见进程： 应用程序不能交互，但是界面可见。 有点类似activity生命周期的onPause


* Service process

> 服务进程： 应用程序里面运行着一个服务

* Background process

> 后台进程: 应用程序被最小化了(home)


* Empty process

> 空进程：应用程序里面没有任何活动的组件（activity \ service）


前台进程 > 可见进程 > 服务进程 > 后台进程 > 空进程

##5.开启与停止服务
    
    
	public void start(View v){
		startService(new Intent(this , ServiceDemo.class));
	}
	

	public void stop(View v){
		stopService(new Intent(this , ServiceDemo.class));
	}

##6.为什么要使用服务
1. 可以让进程所处的优先级有所保障。
2. 可以长久的在后台执行逻辑操作，即使所有的activity都销毁了，也不影响。


场景：
1. 在后台检测设备接入状况
2. 在后台执行联网数据请求(类似股票软件)
3. 音乐播放器

##7.绑定服务第一种
###Extending the Binder class 第一种
> 1 先写一个继承Service类

> 2.在Service中写一个public类继承Binder,在这个类中写个方法返回具服务类的对象实例

> 3.在onBind 中返回 这个内部类的实例

> 4.在service中写几个public的方法提供给子类调用必须为public的
    
            
        /**
         * Created by  刘楠 on 2016-02-28 14:19.
         */
        public class LocalService extends Service {
            private static final String TAG = "LocalService";
            //声明绑定的类
            private LocalBinder mBinder = new LocalBinder();
            //给客户端使用
            private final Random mGenerator = new Random();
        
            /*
            第一种继承Binder类
             */
            public class LocalBinder extends Binder {
        
                public LocalService getServcie() {
                    Log.d(TAG, "==getServcie==");
                    return LocalService.this;
                }
        
            }
        
            @Nullable
            @Override
            public IBinder onBind(Intent intent) {
                Log.d(TAG, "==IBinder==");
                return mBinder;
            }
        
            @Override
            public void onCreate() {
                super.onCreate();
                Log.d(TAG, "==onCreate==");
            }
        
        
            @Override
            public int onStartCommand(Intent intent, int flags, int startId) {
                Log.d(TAG, "==onStartCommand==");
                return super.onStartCommand(intent, flags, startId);
            }
        
            /**
             * 返回一个随机数
             *
             * @return
             */
            public int getRandom() {
                Log.d(TAG, "==getRandom==");
                return mGenerator.nextInt(500);
            }
        
        
            @Override
            public boolean onUnbind(Intent intent) {
                Log.d(TAG, "==onUnbind==");
                return super.onUnbind(intent);
            }
        
            @Override
            public void onDestroy() {
                super.onDestroy();
                Log.d(TAG, "==onDestroy==");
            }
        }




> 5.在Manifest.xml中注册service
> 
     <service android:name=".serivice.LocalService"/>

> 6.在客户端的Activity中绑定service bindService(intnet,ServiceConnection,Context.BIND_AUTO_CREATE)
>
> 7.写一个类实现ServiceConnection接口,在方法中完成获取Service类的实例

> 8.调用服务中的方法

> 9.解除绑定

    布局
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">
    
        <Button
            android:onClick="bind1"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="绑定服务1"/>
    
    
        <TextView
            android:id="@+id/tv"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"/>
    
        <Button
            android:onClick="show"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="调用方法"/>
    
        <Button
            android:onClick="unBind"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="解绑绑定服务1"/>
    </LinearLayout>
>客户端类
        
            Activity
            
        /**
         * 绑定服务
         */
        public class MainActivity extends AppCompatActivity {
            private static final String TAG ="MainActivity" ;
            //服务
            private LocalService mService;
        
            private boolean mbound = false;
        
            private MyServiceConnection mConection;
        
        
            private TextView tv;
        
            @Override
            protected void onCreate(Bundle savedInstanceState) {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.activity_main);
                Log.d(TAG, "=====MainActivity==onCreate=====");
        
                tv = (TextView) findViewById(R.id.tv);
            }
        
            /**
             * 绑定服务
             * @param view 按键
             */
            public void bind1(View view){
                Log.d(TAG, "=====MainActivity==bind1=====");
                Intent intent = new Intent(this,LocalService.class);
        
                if(mConection==null){
                    mConection = new MyServiceConnection();
                }
        
                 mbound = bindService(intent, mConection, Context.BIND_AUTO_CREATE);
        
                Log.d(TAG, "=====MainActivity==flag=====" + mbound);
        
        
            }
        
            public   void show(View v) {
        
                if (mbound) {
                    int random = mService.getRandom();
                    tv.setText(random + "");
                }
        
            }
        
        
            /**
             * 服务连接类
             */
            private class MyServiceConnection implements  ServiceConnection{
        
                @Override
                public void onServiceConnected(ComponentName name, IBinder service) {
                    LocalService.LocalBinder mBinder= (LocalService.LocalBinder) service;
        
                    mService = mBinder.getServcie();
                    Log.d(TAG, "=====MainActivity==MyServiceConnection==onServiceConnected===");
        
        
                }
        
                @Override
                public void onServiceDisconnected(ComponentName name) {
                    Log.d(TAG,"=====MainActivity==MyServiceConnection==onServiceDisconnected===");
                    mbound=false;
                }
            }
        
            /**
             * 解除绑定
             * @param v
             */
            public  void unBind(View v){
                Log.d(TAG,"=====MainActivity==unBind======");
                if(mService!=null) {
                    unbindService(mConection);
                    mConection=null;
        
                }
            }
        
            /**
             * 销毁
             */
            @Override
            protected void onDestroy() {
                Log.d(TAG,"=====MainActivity==onDestroy======");
                super.onDestroy();
                if(mService!=null&&mConection!=null) {
                    unbindService(mConection);
                    mConection=null;
        
                }
            }
        }

以上的方式如果只要给自己程序用同时不要把一些方法私有化的话都可以在Service中写public的方法,使用这种方式


##8.绑定服务第二种
> 使用Messenger与Handler的机制处理,这种方式是一种单线程的,不安全的,目前很少有人用了,会造成排队现象

>Service
>
        
        /**
     * Created by  刘楠 on 2016-02-28 16:29.
     */
    public class MessengerService extends Service {
    
        public static final int MSG_SAY_HELLO = 1;
    
        Messenger mMessenger = new Messenger(new IncomingHandler());
    
        public class  IncomingHandler extends Handler{
            @Override
            public void handleMessage(Message msg) {
                switch (msg.what){
    
                    case MSG_SAY_HELLO:
                        Toast.makeText(getApplicationContext(),"hello Android===handleMessage==",Toast.LENGTH_SHORT).show();
    
    
                }
            }
        }
    
        /**
         *
         * @param intent 意图
         * @return
         */
        @Nullable //参数可以为空
        @Override
        public IBinder onBind(Intent intent) {
            Toast.makeText(getApplicationContext(), "===onBind====", Toast.LENGTH_SHORT).show();
            return mMessenger.getBinder();
        }
    }

>清单文件


    注册
    <service android:name=".service.MessengerService"/>

>客户端

            
        public class MainActivity extends AppCompatActivity {
        
            private Messenger mService;
        
            private MyServiceConnection mServiceConnection;
        
            @Override
            protected void onCreate(Bundle savedInstanceState) {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.activity_main);
            }
        
            /**
             * 界面可见时,绑定Service
             */
            @Override
            protected void onStart() {
                super.onStart();
                Intent intent = new Intent();
                intent.setClass(this, MessengerService.class);
        
                if(mServiceConnection==null){
                    mServiceConnection = new MyServiceConnection();
                }
                //绑定
                bindService(intent, mServiceConnection, Context.BIND_AUTO_CREATE);
            }
        
            private  class  MyServiceConnection implements  ServiceConnection{
        
        
                @Override
                public void onServiceConnected(ComponentName name, IBinder service) {
        
        
                    mService = new Messenger(service);
                }
        
                @Override
                public void onServiceDisconnected(ComponentName name) {
        
                }
            }
        
            /**
             * 点击事件
             * @param v
             */
            public void sayHello(View v) {
        
                Message msg = Message.obtain();
                msg.what=MessengerService.MSG_SAY_HELLO;
                //发送消息
                try {
                    mService.send(msg);
                } catch (RemoteException e) {
                    e.printStackTrace();
                }
            }
        
            @Override
            protected void onDestroy() {
                super.onDestroy();
                if(mService!=null&&mServiceConnection!=null) {
                    unbindService(mServiceConnection);
                    mServiceConnection=null;
                }
            }
        }

##9.使用普通接口完成


- 使用接口的方式建立Service
>接口
    
    /**
     * Created by  刘楠 on 2016-02-28 18:25.
     * 接口定义方法让Service中自定义的Binder来实现这个接口,同时调用Service中公用与私有方法
     */
        public interface IRemoteService {
        
            public void display(String name,int age);
        
            public void gogo();
        
        }
> 建立Service

            
        /**
         * Created by  刘楠 on 2016-02-28 18:20.
         */
        public class LocalService extends Service {
            //自定义的IBinder对象
            private LocalBinder mLocalBinder= new LocalBinder();
        
        
            /**
             * 自定义的Binder
             * 对面接头的内线
             */
            public class LocalBinder extends Binder implements IRemoteService {
                //接口的方法
                @Override
                public void display(String name, int age) {
                    //调用Service中的方法
                    show(name,age);
                }
                //接口的方法
                @Override
                public void gogo() {
                    //调用Service中的方法
                    go();
                }
                //这里算自定义的方法
                public void showData(){
                    Toast.makeText(getApplicationContext(),new Date().toString(),Toast.LENGTH_SHORT).show();
                }
            }
        
        
            private static final String TAG ="LocalService" ;
        
            @Nullable
            @Override
            public IBinder onBind(Intent intent) {
                Log.d(TAG,"=====onBind=======");
                return mLocalBinder;
            }
        
            @Override
            public void onCreate() {
                super.onCreate();
                Log.d(TAG,"=====onCreate=======");
            }
        
            @Override
            public int onStartCommand(Intent intent, int flags, int startId) {
        
                Log.d(TAG,"=====onStartCommand=======");
                return super.onStartCommand(intent, flags, startId);
            }
        
            @Override
            public boolean onUnbind(Intent intent) {
                Log.d(TAG,"=====onUnbind=======");
                return super.onUnbind(intent);
            }
        
            @Override
            public void unbindService(ServiceConnection conn) {
                super.unbindService(conn);
                Log.d(TAG, "=====unbindService=======");
            }
        
            @Override
            public void onDestroy() {
                super.onDestroy();
                Log.d(TAG, "=====onDestroy=======");
            }
        
            /*
                    上面会调用这个方法
                 */
            private void show(String name,int age){
                Toast.makeText(getApplicationContext(),"姓名:"+name+"年龄:"+age,Toast.LENGTH_SHORT).show();
                Log.d(TAG, "====service=show======");
            }
        
            /*
            上面的Binder会调用这个方法
             */
        
            private void go(){
                Toast.makeText(getApplicationContext(),"gogogogogogoggog",Toast.LENGTH_SHORT).show();
                Log.d(TAG, "====service=go======");
            }
        }
>布局就5个按键

        <?xml version="1.0" encoding="utf-8"?>
        <LinearLayout
            xmlns:android="http://schemas.android.com/apk/res/android"
            android:orientation="vertical"
            android:layout_width="match_parent"
            android:layout_height="match_parent">
        
            <Button
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:onClick="bind"
                android:text="绑定bindService服务"/>
        
            <Button
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:onClick="callMethod"
                android:text="调用服务中的方法"/>
        
            <Button
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:onClick="unBind"
                android:text="解除unBindService绑定"/>
        
            <Button
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:onClick="start"
                android:text="使用startService开始服务"/>
            <Button
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:onClick="stop"
                android:text="使用stopService停止服务"/>
        </LinearLayout>
>Activity
    
    
    public class MainActivity extends AppCompatActivity {
        private static final String TGA ="MainActivity" ;
        //连接的类
        private MyServcieConnection mConnection;
        //IBinder对象
        private LocalService.LocalBinder mLocalBinder;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
        }
    
    
    
    
        /**
         * 绑定服务
         *
         * @param view 按键
         */
        public void bind(View view) {
            Log.d(TGA, "========Activity===bind======");
            Intent intent = new Intent();
    
            intent.setClass(this, LocalService.class);
            if(mConnection==null){
                mConnection = new MyServcieConnection();
            }
            bindService(intent, mConnection, Context.BIND_AUTO_CREATE);
        }
    
        /**
         * 调用服务中的方法
         *
         * @param view 按键
         */
        public void callMethod(View view) {
            Log.d(TGA,"========Activity===callMethod======");
            if(mLocalBinder!=null){
                //调用方法
                mLocalBinder.display("张三",18);
                mLocalBinder.gogo();
                mLocalBinder.showData();
    
            }
        }
    
        /**
         * 解除绑定服务
         *
         * @param view 按键
         */
        public void unBind(View view) {
            Log.d(TGA,"========Activity===unBind======");
            if(mConnection!=null){
                unbindService(mConnection);
                mConnection=null;
                mLocalBinder=null;
            }
        }
    
        /**
         * 开始服务
         *
         * @param view 按键
         */
        public void start(View view) {
            Log.d(TGA,"========Activity===start======");
            Intent intent = new Intent();
            intent.setClass(this, LocalService.class);
            startService(intent);
        }
    
        /**
         * 停止服务
         *
         * @param view 按键
         */
        public void stop(View view) {
            Log.d(TGA,"========Activity===stop======");
            //停止服务
            Intent intent = new Intent();
            intent.setClass(this, LocalService.class);
            stopService(intent);
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            Log.d(TGA, "========Activity===onDestroy======");
           /* if(mConnection!=null){
                unbindService(mConnection);
                mConnection=null;
                mLocalBinder=null;
                return;
            }
            //停止服务
            Intent intent = new Intent();
            intent.setClass(this, LocalService.class);
            stopService(intent);*/
        }
    
        /**
         * 服务连接类
         */
        private class MyServcieConnection implements ServiceConnection{
    
            @Override
            public void onServiceConnected(ComponentName name, IBinder service) {
                Log.d(TGA,"========Activity===onServiceConnected======");
                mLocalBinder = (LocalService.LocalBinder) service;
            }
    
            @Override
            public void onServiceDisconnected(ComponentName name) {
                Log.d(TGA,"========Activity===onServiceDisconnected======");
            }
        }
    }


>注册

        <!--注册-->
        <service android:name=".service.LocalService"/>


>结果:

    02-28 06:39:24.895 4350-4350/? D/MainActivity: ========Activity===bind======
    
    02-28 06:39:24.968 4350-4350/? D/LocalService: =====onCreate=======
    02-28 06:39:24.969 4350-4350/? D/LocalService: =====onBind=======
    02-28 06:39:24.985 4350-4350/? D/MainActivity: ========Activity===onServiceConnected======
    02-28 06:39:28.625 4350-4350/? D/MainActivity: ========Activity===callMethod======
    
    02-28 06:39:28.631 4350-4350/? D/LocalService: ====service=show======
    02-28 06:39:28.637 4350-4350/? D/LocalService: ====service=go======
    ace 0xeb9b54a0, error=EGL_SUCCESS
    02-28 06:39:30.906 4350-4350/? D/MainActivity: ========Activity===unBind======
    
    02-28 06:39:30.937 4350-4350/? D/LocalService: =====onUnbind=======
    02-28 06:39:30.937 4350-4350/? D/LocalService: =====onDestroy=======
    
    02-28 06:39:32.721 4350-4368/? V/RenderScript: 0xe1f0e200 Launching thread(s), CPUs 4
    02-28 06:39:35.827 4350-4350/? D/MainActivity: ========Activity===start======
    
    02-28 06:39:35.846 4350-4350/? D/LocalService: =====onCreate=======
    02-28 06:39:35.846 4350-4350/? D/LocalService: =====onStartCommand=======
    02-28 06:39:36.878 4350-4350/? D/MainActivity: ========Activity===stop======
    
    02-28 06:39:36.902 4350-4350/? D/LocalService: =====onDestroy=======

##10.AIDL (AIDL-Android Interface Definition Language)

> AIDL可以同时处理多个线程的请求,并且是线程安全的.

> To use AIDL directly, you must create an .aidl file that defines the programming interface. The Android SDK tools use this file to generate an abstract class that implements the interface and handles IPC, which you can then extend within your service.


要使用AIDL必须建立一个扩展名为.aidl的文件,SDK会自动生成对象的抽象类同是实现接口与Handles IPC通信,在Service中可以选择继承

>例如:支付宝,就只是暴露了,接口给商家使用,里面具体怎么实现并没有暴露

>将上面的接口后缀名改为.aidl

* 更改Serive中的自定义IBinder

     public class LocalBinder extends  IRemoteService.Stub {

* 更改客户端 中声明不在写原来的binder

           更改这个为
         private LocalService.LocalBinder mLocalBinder;
        
>更改后

        //IRemoteService
        private IRemoteService mIRemoteService;

>Activity

                
        public class MainActivity extends AppCompatActivity {
            private static final String TGA ="MainActivity" ;
            //连接的类
            private MyServcieConnection mConnection;
            //IRemoteService
            private IRemoteService mIRemoteService;
        
            @Override
            protected void onCreate(Bundle savedInstanceState) {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.activity_main);
            }
        
        
        
        
            /**
             * 绑定服务
             *
             * @param view 按键
             */
            public void bind(View view) {
                Log.d(TGA, "========Activity===bind======");
                Intent intent = new Intent();
        
                intent.setClass(this, LocalService.class);
                if(mConnection==null){
                    mConnection = new MyServcieConnection();
                }
                bindService(intent, mConnection, Context.BIND_AUTO_CREATE);
            }
        
            /**
             * 调用服务中的方法
             *
             * @param view 按键
             */
            public void callMethod(View view) {
                Log.d(TGA,"========Activity===callMethod======");
                if(mIRemoteService!=null){
                    //调用方法
                    try {
                        mIRemoteService.display("张三",18);
                        mIRemoteService.gogo();
                    } catch (RemoteException e) {
                        e.printStackTrace();
                    }
        
        
        
                }
            }
        
            /**
             * 解除绑定服务
             *
             * @param view 按键
             */
            public void unBind(View view) {
                Log.d(TGA,"========Activity===unBind======");
                if(mConnection!=null){
                    unbindService(mConnection);
                    mConnection=null;
                    mIRemoteService=null;
                }
            }
        
            /**
             * 开始服务
             *
             * @param view 按键
             */
            public void start(View view) {
                Log.d(TGA,"========Activity===start======");
                Intent intent = new Intent();
                intent.setClass(this, LocalService.class);
                startService(intent);
            }
        
            /**
             * 停止服务
             *
             * @param view 按键
             */
            public void stop(View view) {
                Log.d(TGA,"========Activity===stop======");
                //停止服务
                Intent intent = new Intent();
                intent.setClass(this, LocalService.class);
                stopService(intent);
            }
        
            @Override
            protected void onDestroy() {
                super.onDestroy();
                Log.d(TGA, "========Activity===onDestroy======");
        
            }
        
            /**
             * 服务连接类
             */
            private class MyServcieConnection implements ServiceConnection{
        
                @Override
                public void onServiceConnected(ComponentName name, IBinder service) {
                    Log.d(TGA,"========Activity===onServiceConnected======");
                    mIRemoteService =IRemoteService.Stub.asInterface(service);
                }
        
                @Override
                public void onServiceDisconnected(ComponentName name) {
                    Log.d(TGA,"========Activity===onServiceDisconnected======");
                }
            }
        }

这种aidl的方式只能调用Serivce接口中的方法,不能调用Service和自定义Binder中特有的方法
常用于给第三方的应用提供服务
* 通过 


         IRemoteService.Stub.asInterface(service)
方法得到aidl定义的接口实例

##11.startService与bindService的生命周期
* startService与stopService
>startService
            
            onCreate---创建服务时只执行一次
            onStartCommand  ----每次开启服务都会调用

>stopService
            
           onDestory--停止服务时多次调用,只会执行一次

*bindService与unBindServcie
> bindService


        onCreate---创建服务时只执行一次
        onBind----多次调用,只执行一次

>unBindService

        onUnbind----解除时,只执行一次
        onDestory----只执行一次


