# AndroidStudy
Android学习
#广播BroadcastReceiver#
##1.广播简介##
>BroadcastReceiver用于接收程序包括用户开发的程序和系统内建程序所发出的Broadcast 


Intent,与应用程序的启动Activity,Service相同的是程序BroadcastReceiver也只需要两步

1. 创建需要启动的BroadcastReceiver的Intent
2. 调用Context的sendBroadcast()或者sendOrderBroadcast()方法来启动指定的BroadcastReceiver

当应用程序发出一个Broadcast Intent之后,所有匹配这个Intent的BroadcastReceiver都有可能被启动

>与Activity,Service具有完整的生命周期不同,BroadcastReceiver本质上只是一个系统级的监听级---它专门用来监听各个程序所发出的Broadcast

##2.建立广播##
建立广播只需要3步就可以了

1. 写一个JAVA类继承BroadcastReceiver

2. 重写OnReceive方法

3. 在Manifest.xml中配置<receiver />

        public class MyTestBroadcastReceiver extends BroadcastReceiver {

        @Override
        public void onReceive(Context context, Intent intent) {

             }
          }

>Manifest.

        <!--配置广播-->
        <receiver android:name=".MyTestBroadcastReceiver"/>

4.第3步或者使用resisterReceiver(receiver,filter) 

##3 一些注意事项##

每次系统Broadcast事件发生之后,系统就会创建对应 的BroadcastReceiver实例,并自动触发它的onReceiver()方法,onReceiver()方法执行完成后,就会销毁BroadcastReceiver对象实例

不要在BroadcastReceiver的onReceiver方法中做耗时的事,如果10秒不能完成就会发生ANR
(Application Not Response)的对话框

如果确定需要可以根据Broadcast来完成一个耗时的事,可心考虑通过Intent启动一个Service来完成操作.不应该使用新线程去完成,因为BroadcastReceiver的本向生命周期很短,可能出现子线程还没结束,BroadcastReceiver就已经退出了

##4.发送一个简单的广播##

>布局
        
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <Button
        android:onClick="sendMyBroadcast"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="发送广播"/>
    </LinearLayout>

>广播类
    
    public class MyTestBroadcastReceiver extends BroadcastReceiver {

    private static final String TAG ="MyTestBroadcastReceiver" ;

    @Override
    public void onReceive(Context context, Intent intent) {

        String msg = intent.getAction()+"\n"+intent.getStringExtra("msg");
        Log.d(TAG,msg);
        Toast.makeText(context,msg,Toast.LENGTH_SHORT).show();
        }
    }

>Manifest文件配置广播

    <!--配置广播-->
        <receiver android:name=".MyTestBroadcastReceiver">
            <intent-filter>
                <!--配置这个广播响应的Intent-->
                <action android:name="com.sms.MY_BROADCAST"/>
            </intent-filter>
        </receiver>

>Activity

    /**
     *  发送广播
     * @param v 按键
     */
    public void sendMyBroadcast(View v){

        Intent intent = new Intent();
        //设置广播接收都的Action
        intent.setAction("com.sms.MY_BROADCAST");
        //数据信息
        intent.putExtra("msg","这是个简单的广播");
        //发送广播
        sendBroadcast(intent);
    }

##5.广播分类

Broadcast分为:
>普通广播(Normarl Broadcast):效率高,缺点接收都不能将处理结果传递给下一个接收者,无法终止广播
>
>有序广播(Ordered Broadcast):可以通过配置优先级

         <intent-filter android:priority="">
数据越大优先级越高,有充广播可以终止广播
        abortBroadcast();终止广播

Context提供的两具方法用于发送广播
    sendBroadcast(intent):发送普通广播
    sendOrderedBroadcast(intent);发送有序广播

系统会根据优先级另按顺序逐个执行接收者,优先收到Broadcastr接收者可以终止广播,
如果终止广播,那么比这个优先级低的后面的都将收不到广播

优先收到Broadcast的接收者,可以通过

        setResultExtras();
        setResultData();
等方法将处理结果存入Broadcast中,然后传递给下一个接收者,下一个接收者通过

     Bundle bundle = getResultExtras(true);

可以获取上一个接收都存入的数据
##6.发送一个有序广播

>布局

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <Button
        android:onClick="sendMyBroadcast"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="发送广播"/>
    </LinearLayout>


>2个广播接收者

        public class FirstBroadcastReceiver extends BroadcastReceiver {

    private static final String TAG = "FirstBroadcastReceiver";

    @Override
    public void onReceive(Context context, Intent intent) {

        //获取发送的数据

        //获取
        int msg = intent.getIntExtra("data",0);
        Toast.makeText(context, "第一个收到的:"+msg, Toast.LENGTH_SHORT).show();
        Log.d(TAG, msg + "");
        //将设置向下发的内容
        Bundle bundle = new Bundle();
        bundle.putInt("data",msg-333);
        //将bundle放入结果,继承向下发
        setResultExtras(bundle);
        //终止广播
        //abortBroadcast();

        }
    }


>第二个

        public class SecondBroadcastReceiver extends BroadcastReceiver {

    private static final String TAG = "SecondBroadcastReceiver";

    @Override
    public void onReceive(Context context, Intent intent) {

        Bundle bundle = getResultExtras(true);

        int msg = bundle.getInt("data");
        Log.d(TAG, msg+"");
        Toast.makeText(context, "第一个放入的消息:"+msg, Toast.LENGTH_SHORT).show();
     }
    }
>Manifest.xml

      <!--配置广播-->
        <receiver android:name=".MyTestBroadcastReceiver">
            <intent-filter android:priority="11">
                <!--配置这个广播响应的Intent-->
                <action android:name="com.sms.qq"/>
            </intent-filter>
        </receiver>
            <!--配置第一个广播优先级比第二个高-->
        <receiver android:name=".FirstBroadcastReceiver">

            <intent-filter android:priority="500">
            <action android:name="com.sms.MY_BROADCAST"/>
            </intent-filter>
        </receiver>
        <!--配置第二个广播接收者优先级比第一个低-->
        <receiver android:name=".SecondBroadcastReceiver">
            <intent-filter android:priority="200">
                <action android:name="com.sms.MY_BROADCAST"/>
            </intent-filter>

        </receiver>

>Activity
    
    /**
     *  发送广播
     * @param v 按键
     */
    public void sendMyBroadcast(View v){

        Intent intent = new Intent();
        //设置广播接收都的Action
        intent.setAction("com.sms.MY_BROADCAST");
        //数据信息
        intent.putExtra("data", 1000);
        //发送有序广播
        sendOrderedBroadcast(intent,null);

    }
    
>结果:

    02-26 05:40:37.557 13750-13750/? D/FirstBroadcastReceiver: 1000
    02-26 05:40:37.591 13750-13750/? D/SecondBroadcastReceiver: 100
    02-26 05:44:35.746 14193-14193/? D/FirstBroadcastReceiver: 1000
    02-26 05:44:35.760 14193-14193/? D/SecondBroadcastReceiver: 667



##7.系统常用广播##


- android.intent.action.ACTION_POWER_CONNECTED 系统连接电源
- android.intent.action.ACTION_POWER_DISCONNECTED:系统断开电源
- android.intent.action.BATTERY_LOW低电量
- android.intent.action.BOOT_COMPLETED:系统启动完成
- android.intent.action.PACKAGE_ADDED系统 添加新的包,安装了新的APP
- android.intent.action.PACKAGE_CHANGED系统的包发生了变化
- android.intent.action.PACKAGE_REMOVED系统包删除
- android.intent.action.TIMEZONE_CHANGED系统时区发生变化 
- android.intent.action.ACTION_SHUTDOWN系统被关闭

##8.练习一 监听手机SD卡状态
>建立一个类继承BroadcastReceiver
>重写onReceiver方法

        /**
 * 监听SD卡状态
 * 
 * @author 刘楠
 * 
 *         2016-2-27上午9:20:54
 */
public class SdCardReceiver extends BroadcastReceiver {

    	@Override
    	public void onReceive(Context context, Intent intent) {
    		//取出Action
    		String action = intent.getAction();
    		//判断
    		if ("android.intent.action.MEDIA_MOUNTED".equals(action)) {
    			
    			Toast.makeText(context, "SD已经挂载成功", Toast.LENGTH_SHORT).show();
    			
    		} else if ("android.intent.action.MEDIA_UNMOUNTED".equals(action)) {
    			
    			Toast.makeText(context, "SD卡拔出了,照像,图片,下载功能将不可用", Toast.LENGTH_SHORT).show();
    		}
    
    	}
    
        }

        

>注册

     <!--注册广播接收者  -->
        
        <receiver android:name="com.itheima.sdstatus.SdCardReceiver">
            <intent-filter >
                <!-- SD卡拔出 -->
                <action android:name="android.intent.action.MEDIA_UNMOUNTED"/>
                <!-- SD卡挂载 -->
                <action android:name="android.intent.action.MEDIA_MOUNTED"/>
                <!--这里要写文件  -->
                <data android:scheme="file"/>
            </intent-filter>
        </receiver>

##9.练习二 IP打电话

>建立PhoneCallReceiver
>重写onReceive方法

    
    /**
 * 监听电话
	
 * @author 刘楠
 *
 * 2016-2-27下午12:17:53
 */
public class PhoneReceiver extends BroadcastReceiver {

    	private static final String TAG = "PhoneReceiver";
    
    	@Override
    	public void onReceive(Context context, Intent intent) {
    		
    		Log.d(TAG, "拔打的电话"+getResultData());
    		String number = getResultData();
    		String prefix = "17951";
    		if(number.startsWith("0")){
    			number=prefix+number;
    			//放回去
    			setResultData(number);
    		}
    
    	}
    
         }
>注册
        
        <!--注册Receiver  -->
        <receiver android:name="com.itheima.ipcall.PhoneReceiver">
            <intent-filter>
                <!--监听打电话  -->
                <action android:name="android.intent.action.NEW_OUTGOING_CALL"/>
            </intent-filter>
        </receiver>
>添加权限
        
        <uses-permission android:name="android.permission.PROCESS_OUTGOING_CALLS"/>

##10.开机启动一个界面

>建立BootReceiver
>重写onReceive方法

    
    /**
 * 监听开机完成
	
 * @author 刘楠
 *
 * 2016-2-27下午12:35:37
 */
public class BootReceiver extends BroadcastReceiver {

        	private static final String TAG = "BootReceiver";
        
        	@Override
        	public void onReceive(Context context, Intent intent) {
        		Log.d(TAG, "开机完成了");
        
        		Toast.makeText(context, "开机完成了", Toast.LENGTH_SHORT).show();
        		//开机后打开一个界面
        		Intent in = new Intent(context, MainActivity.class);
        		//这里的context不是activity的context， 所以没有栈的概念，那么默认启动的这个activity就不知道放到哪个栈中，
        		in.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        		context.startActivity(in);
        	}
        
        }
>注册

    <!-- 注册开机完成Receiver开机启动 -->
        <receiver android:name="com.itheima.bootcomplete.BootReceiver" >
            <intent-filter>
                <!--注册监听的是哪个  -->
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
            </intent-filter>
        </receiver>
    


>添加权限

        <!--  添加权限-->
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>

##11.监听了软件的安装与卸载


>建立PackageReceiver

>重写onReceive方法

         @Override
    	public void onReceive(Context context, Intent intent) {
    		// 获取动作
    		String action = intent.getAction();
    		//Log.d(TAG, "包名:" + intent.getData());
    		if (Intent.ACTION_PACKAGE_ADDED.equals(action)) {
    			Log.d(TAG, "包名:" + intent.getData() + "安装了");
    		} else if (Intent.ACTION_PACKAGE_REMOVED.equals(action)) {
    			Log.d(TAG, "包名:" + intent.getData() + "卸载了");
    		}
    	}

>>注册
    
        <!-- 注册Package的安装的删除 -->
            <receiver android:name="com.itheima.packageaddandremove.PackageReceiver" >
                <intent-filter>
                    <!--安装  -->
                    <action android:name="android.intent.action.PACKAGE_ADDED"/>
                    <!--  卸载-->
                    <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                    <!--包数据  -->
                    <data android:scheme="package"/>
                </intent-filter>
            </receiver>


##12.短信内容窃听器

    
    
		<receiver android:name="com.itheima.sms.SmsReceiver">
            <intent-filter >
 				注意： 这里的动作，只有去找上层应用mms源码的清单文件，才可以找到
                <action android:name="android.provider.Telephony.SMS_RECEIVED"/>
            </intent-filter>
        </receiver>

		解析短信：

			Telephony.java 这个类，里面有一个方法getMessagesFromIntent 

			可以到一个网站去找 www.grepcode.com   

			或者使用自己的源码去找。
>建立SMSReceiver

>重写onReceive方法
    
        @Override
    	public void onReceive(Context context, Intent intent) {
    		//获取短信管理者集合
    		Object[] message = (Object[]) intent.getSerializableExtra("pdus");
    		
    		for (Object object : message) {
    			//轮换为字节数组
    		byte [] pdus =	(byte[]) object;
    		
    		//短信信息
    		SmsMessage smsMessage = SmsMessage.createFromPdu(pdus);
    		Log.d(TAG, "收到一条短信:发件人-->"+smsMessage.getOriginatingAddress()+",内容:"+smsMessage.getMessageBody());
    		}
    
    	}

>注册

    <!--注册短信监听者  -->   
        <receiver android:name="com.itheima.smsreceiver.SMSReceiver">
            <intent-filter >
                <!--监听收到的短信  -->
                <action android:name="android.provider.Telephony.SMS_RECEIVED"/>
            </intent-filter>
        </receiver>
> 权限

    <uses-permission android:name="android.permission.RECEIVE_SMS"/>

##13.广播接收者在不同的版本特点
    
    * 2.3

	1.  在DDMS里面结束了进程，广播到来时，依然能够收到广播
	2.  即使 force stop 强制停止应用，也能继续收到广播

    * 4.1.2

	1.  在DDMS里面结束了进程，广播到来时，依然能够收到广播
	2.  在设置应用里面 找到具体应用， force stop 强制停止应用，将不会继续收到广播

##14.发送有序广播与最 终接收者
    
>发送广播
    
        	/**
    	 * 发送有序广播
    	 * @param v 当前按键
    	 */
    	
    	public void send(View v){
    		
    		Intent intent = new Intent();
    		intent.setAction("com.CCTV.NEWS");
    		/*
    		 * Intent intent,  
    		 * UserHandle user,  
                String receiverPermission,  权限
                BroadcastReceiver resultReceiver 最终接收者,广播终止也能收到, 
                Handler scheduler,   
                int initialCode,  初始码
                String initialData, 初始码数据
    			Bundle initialExtras 额外的数据
    		 */
    		sendOrderedBroadcast(intent, null, null, null, 1, "向全国人民每人发100元", null);
    	}

>接收广播

>省级
    
            public class ProvinceReceiver extends BroadcastReceiver {
        
        	private static final String TAG = "CCTV";
        
        	@Override
        	public void onReceive(Context context, Intent intent) {
        		
        			Log.d(TAG, "省级接收者收到==>"+getResultData());
        			
        			setResultData("向全省人民每人发80元");
        	}
        
        }
    
>市级
    
    
	private static final String TAG = "CCTV";

	@Override
	public void onReceive(Context context, Intent intent) {
		
			Log.d(TAG, "市级接收者收到==>"+getResultData());
			setResultData("向全市人民每人发50元");
	}
>区级

    private static final String TAG = "CCTV";

	@Override
	public void onReceive(Context context, Intent intent) {
		
			Log.d(TAG, "区级接收者收到==>"+getResultData());
			setResultData("向全区人民每人发30元");
	}

>农民
    private static final String TAG = "CCTV";

	@Override
	public void onReceive(Context context, Intent intent) {
		
			Log.d(TAG, "农民接收者收到==>"+getResultData());
	}


>注册

    <!--注册广播接收者  -->
        <receiver android:name="com.itheima.cctvreceiver.ProvinceReceiver">
            <intent-filter android:priority="1000">
                <action android:name="com.CCTV.NEWS"/>
            </intent-filter>
        </receiver>
        <receiver android:name="com.itheima.cctvreceiver.CityReceiver">
            <intent-filter android:priority="800">
                <action android:name="com.CCTV.NEWS"/>
            </intent-filter>
        </receiver>
        <receiver android:name="com.itheima.cctvreceiver.ZoneReceiver">
            <intent-filter android:priority="500">
                <action android:name="com.CCTV.NEWS"/>
            </intent-filter>
        </receiver>
        <receiver android:name="com.itheima.cctvreceiver.PeopleReceiver">
            <intent-filter android:priority="100">
                <action android:name="com.CCTV.NEWS"/>
            </intent-filter>
        </receiver>

>最终接收者
    
    private static final String TAG = "CCTV";

	@Override
	public void onReceive(Context context, Intent intent) {
		
		Log.d(TAG, "最终用户收到"+getResultData());

	}

>最终发送者

    	public void send(View v){
	
		Intent intent = new Intent();
		intent.setAction("com.CCTV.NEWS");
		
		
	
		
		/*      
		 * Intent intent,  
            String receiverPermission,  权限
            BroadcastReceiver resultReceiver 最终接收者,广播终止也能收到, 
            Handler scheduler,   
            int initialCode,  初始码
            String initialData, 初始码数据
			Bundle initialExtras 额外的数据
		 */
		sendOrderedBroadcast(intent, null, new ResultReceiver(),null, 1, "向全国人民每人发100元", null);
	}


##15.getResultData()与intent.getData()使用与区别
1. getResultData()只能接收有序广播,并获取数据
2. 有序广播的initileData有数据,getResultData()可以获取数据
3. 最终接收者可以不用在清单文件中配置
4. 有序广播要设置优先级android:priority="" 最大1000,最小-1000


##16.广播的类型

* 有序广播

> 广播接收者收到广播有先后顺序之分，到底谁先睡后，由优先级别来指定， 优先级别高的广播接收者可以修改广播的内容，甚至是终止广播的继续传输。 有点类似： 中央文件层层下发

* 无序广播

> 广播接收者收到广播没有顺序之分，大家都是同时收到，也就谈不上修改内容与终止广播 


##17细节处理

1. getResultData只对有序广播起作用 ， 在无序广播里面，通过这个方法拿不到数据， null. 
2. getResultData的数据在哪里赋值呢。通过发送有序广播里面initialData参数去赋值 
3. 向外呼叫电话虽然也是一个有序广播，但是无法终止广播，因为内部有一个最终结果接收者。 无论如何终止都能收到。 但是可以通过一个取巧的办法去设置  

 				setResultData(null);

4. 短信接收也是一个有序广播，可以终止传输。

##18.只能使用代码注册的广播接收者
>使用频率很高的,如电源键,SCREEN_ON/OFF广播,充电状态,Activity退出后不再接收广播

>为什么只能使用代码注册?

 在系统里面有这样的广播接收者，它们出现的频率极高， 如果可以在清单文件里面注册广播接收者，那么就会造成一种后果，一旦广播到来，那么将会有一大批的应用随之启动。 如： 屏幕锁屏和点亮、电池电量变更

>监听器

    @Override
	public void onReceive(Context context, Intent intent) {
		String action = intent.getAction();
		
		if(action.equals(Intent.ACTION_SCREEN_ON)){
		Log.d(TAG, "屏幕点亮了");
		}else{
			Log.d(TAG, "屏幕休眠了");
		}

	}

>注册

    private ScreenReceiver sReceiver;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		
		sReceiver = new ScreenReceiver();
		IntentFilter filter = new IntentFilter();
		//添加动作
		filter.addAction(Intent.ACTION_SCREEN_OFF);
		filter.addAction(Intent.ACTION_SCREEN_ON);
		//注册广播接收者
		registerReceiver(sReceiver, filter);
	}

	
	@Override
	protected void onDestroy() {
        //当界面销毁了之后，记得解除注册广播接收者
		super.onDestroy();
		//解除注册
		unregisterReceiver(sReceiver);
	}


