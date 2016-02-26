# AndroidStudey
Android学习
###Activity （组件）

> 一个界面对应一个activity


###如何在应用程序里面创建多个界面

			 意图过滤器
			 <intent-filter>
				动作 ： 这里声明了应用程序的入口界面
                <action android:name="android.intent.action.MAIN" />

				分类： 指定了再应用程序列表里面有一个图标
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>

	1. 声明一个布局
	2. 定义一个类， 继承activity
		在activity里面的onCreate方法指定显示的布局  setContentView(R.layout.xxx);
	3. 登记注册activity



###人品计算器

1. 指定一个界面全屏或者去掉标题栏

> 指定一个activity的theme即可  android:theme="@android:style/Theme.Light.NoTitleBar.Fullscreen"

2. 从logo界面，自动跳转到main界面 （3秒）

		new Thread(){
			public void run() {
				SystemClock.sleep(3000);
				startActivity(new Intent(LogoActivity.this , MainActivity.class));
				
				//一旦跳转过了，那么久结束自己的界面
				finish();
				
			};
		}.start();


####数据传递

* 简单数据

		intent.putExtra(key , value);

		----------------------------

		Intent intent = getIntent();
		intent.getXXXExtra(key); // xxx ---- 指的是早前传递的数据类型

* 复杂数据

	1. Serializable 

			这是一个空的接口，只要让javaBean实现这个接口即可，那么就可以传递对象到下一个界面
		
			为什么要求的是Serializable ， 但是穿进去stu也没错呢。
		
			public class Student implements Serializable{

			---------------------------------------------
			
			Intent intent = getIntent();
			Student stu = (Student) intent.getSerializableExtra("stu");

	2. Parcelable
		
			以下是让javaBean实现了parcelable接口的实现方法， 注意： 这些方法都是可以从文档里面查出来的。
			public int describeContents() {
	         return 0;
		     }
		
			 //把数据写到包里面 -- 读写顺序要一致
		     public void writeToParcel(Parcel out, int flags) {
		    	 out.writeString(name);
		         out.writeInt(age);
		     }
		
		     public static final Parcelable.Creator<Student> CREATOR
		             = new Parcelable.Creator<Student>() {
		    	 //从parcel转化数据，形成一个student对象
		         public Student createFromParcel(Parcel in) {
		             return new Student(in);
		         }
		
		         public Student[] newArray(int size) {
		             return new Student[size];
		         }
		     };
		     
		     private Student(Parcel in) {
		    	 name =  in.readString();
		    	 age = in.readInt();
		     }


			//跳转过去，传递一个对象 javaBean
			ArrayList<Student> stus = new ArrayList<Student>();
			Student stu1 = new Student("zhangsan", 18);
			Student stu2 = new Student("zhangsan1", 118);
			Student stu3 = new Student("zhangsan2", 128);
			
			stus.add(stu1);
			stus.add(stu2);
			stus.add(stu3);
			Intent intent = new Intent(this ,OtherActivity.class);
			intent.putExtra("stus", stus);


			--------------------------------
				Intent intent = getIntent();
			ArrayList<Student> stus = intent.getParcelableArrayListExtra("stus");
			
			for (Student stu : stus) {
				Log.d(TAG, "STU=="+stu.toString());
			}
				


###启动界面的两种方式

* 显式意图

> 写法上简单多了，只要指定上下文，和具体的类就可以跳转 

> 能够直接知道跳转的界面

		注册：
			<activity android:name="com.itheima.start.Activity01"></activity>
		跳转：

			Intent intent = new Intent(this , Activity01.class);

			startActivity(intent);




		作用： 一般是启动自己程序的界面，其他程序的界面通过这种方式无法启动

		如果使用了显式手法注册，还想被其他应用程序打开， 那么可以在activity标签里面，加上
		android:exported="true"
			

* 隐式意图

> 写法上稍稍复杂，要指定action 和 data 

> 看不出来跳转的界面，只有运行后才知道


		注册：

				<activity android:name="com.itheima.start.Activity02">
		            <!-- 意图过滤器， 只要满足了action ， 和 category .,那么久启动这个界面 -->
		            <intent-filter >
		                <action android:name="com.itheima.start.action.OPEN02"/>
		                
		                <category android:name="android.intent.category.DEFAULT"/>
		            </intent-filter>
		        </activity>

		跳转：

				Intent intent = new Intent();
				intent.setAction("com.itheima.start.action.OPEN02");
				intent.addCategory("android.intent.category.DEFAULT");
				startActivity(intent);


	作用： 跳转其他的应用程序界面，或者是自己的应用程序界面想被其他的程序打开


	详细参数： 

			action:  动作
			category  分类
			data: 指定数据，  一般关注两个：scheme  和 mimetype 

			android:scheme="itheima" android:path="www.itheima" android:mimeType="text/plain"


###使用隐式意图打开系统程序

1. 打开浏览器

			Intent intent = new Intent();
				
			intent.setAction("android.intent.action.VIEW");
				
			intent.addCategory("android.intent.category.DEFAULT");
				
			intent.setData(Uri.parse("http://www.itheima.com"));
				
			startActivity(intent);

			以上代码全部来自系统上层应用的清单文件

			TextView本身不具备点击， 所以要想onClick方式也起作用

			android:clickable="true"
        	android:onClick="click"

2. 打开短信编写界面

		推荐好友，使用软件

		Intent intent = new Intent();
		
		intent.setAction("android.intent.action.SENDTO");
		intent.addCategory("android.intent.category.DEFAULT");
		intent.setData(Uri.parse("sms:"));
		
		//这里的key，不能乱写，只有去起看一看源码中是怎么接收的，再逆推回来即可
		intent.putExtra("sms_body", "推荐您使用一款软件，我最近都在使用它，挺不错的，叫做《人品计算器》 ,下载地址是： http://www.itheima.com");
		
		startActivity(intent);



###打开下一个界面，然后返回数据

* 场景

> QQ聊天给好友发送图片 (拍照返回、 相册选取)

* 短信助手


		1. 
		Intent intent = new Intent(this , SmsListActivity.class);
		//1. 为了一个结果，启动一个界面、 启动界面等待结果的返回
		startActivityForResult(intent, 1);

		2.
		//2. 设置返回的数据
				Intent intent  = new Intent();
				intent.putExtra("data", data);
				setResult(1, intent);

		3. 
		finish()

		4. 收数据

			//4. 接受数据
		@Override
		protected void onActivityResult(int requestCode, int resultCode, Intent data) {
			super.onActivityResult(requestCode, resultCode, data);
			
			//要记得判空操作，因为有可能用户直接点击了返回键
			if(data == null){
				return ;
			}
			String str = data.getStringExtra("data");
			etContent.setText(str);
		}

### requestCode  和 resultCode

* requestCode

> 请求码 如果一个界面一开始就有多个请求出去，那么回来的时候也会有多个地方的数据返回，但是收取数据的方法只有一个，所以requestCode 可以很好的判断目前到底是哪一个请求回来了。

* resultCode
	
> 结果码， 如果下一个界面有可能返回多种类型的数据，那么就可以根据resultCode来区分到底是什么类型的数据


###Activity生命周期

* 生命周期

> 一类从出生到消亡的过程（时间段）

* 生命周期方法

> 对象从创建到销毁，所调用的方法。


* 创建与销毁

> onCreate 和 onDestroy 数据的保存和回显工作


* 可见与不可见

> onStart 和 onStop  可见的时候播放视频，不可见的时候暂停视频的播放


* 获取焦点和失去焦点

> onResume 和 onPause 

###生命周期总结

*  entire lifetime 

> 完整生命周期 onCreate-- onStart-- onResume -- onPause -- onStop -- onDestroy

* visible lifetime

> 可视生命周期 ：  onStart()  -- onResume -- onPause -- onStop

* foreground lifetime

> 前台生命周期： onResume -- onPause


###横竖屏切换：

1. 直接规定了是什么方向显示

> android:screenOrientation="landscape" |portrait

2. 不指定方向，横屏就横屏显示，竖屏就竖屏显示

> android:configChanges="orientation|screenSize|keyboardHidden"

###任务和栈

* task

> 一个应用程序可以拥有多个界面，一个界面就可以看成是与用户交互的任务 task , 为了方便管理，所以android就使用一个栈来集中管理他们这些任务 ， 如果一个应用程序的所有栈都已经清空了，那么这个应用程序也就完全退出了 ， 一个应用程序可以拥有多个任务栈

* stack

> 栈 ， 是一种数据结构 遵循的是后进先出， 队列 （Queue ） ，先进先出


####Activity启动模式

* standard 

> 这是默认的启动模式，： 启动多少次这个界面，就会有多少个这个界面实例被压入栈中


* singleTop

> 单一顶部模式：如果一个界面的实例已经存在栈顶，那么久不会再继续创建这个界面的实例，而是继续使用这个实例。　只要不是顶部，就与默认的模式一样。

> 场景： 为了避免出现一些奇怪的现象，为了避免让自己启动自己  短信编写界面* 

* singleTask

> 单一任务模式：如果一个界面的实例已经存在栈中，那么再次启动就不会继续创建新的实例，而是继续复用它，并且把它与栈顶之间的其他实例全部移除掉。

> 场景： 为了减轻资源的消耗，在栈中值创建一次实例， 比如： 浏览器的界面

* singleInstance

> 全局唯一模式：  无论创建多少次都只有一个实例，并且这个实例放在一个独立的栈中，里面有且只能有它自己一个实例

> 场景： 为了减轻资源的消耗，在栈中值创建一次实例  比如： 通话界面或者紧急通话界面


###注意：

1. 实际上只要让javaBean实现Serializable 接口就可以了，不管是传递单个对象，或者是传递一个集合，都没问题。
2. 之所以还要讲parcelable , 为了扩展视野， 以为为了以后的工作。

		两个界面之间能不能传递图片  
			resoureId R.drawable.xxx  -- int 
			bitmap ---> 因为bitmap里面实现了parcelable

