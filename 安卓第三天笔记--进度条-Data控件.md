# AndroidStudy
Android学习
安卓第三天笔记--进度条,Data控件
1.通知Notification
通知就是在是上方的状态栏弹出通知消息

<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context=".MainActivity" >

    <Button
        android:onClick="send"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="发送通知" />

</RelativeLayout>


<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >
    
    <TextView 
        android:id="@+id/tv"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="这是通知的跳转页面"/>

    <ImageView 
        android:id="@+id/iv"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:src="@drawable/swift"/>
    
</LinearLayout>


/**
 * 收到通知时,点击通知打开的Activity
	
 * @author 刘楠
 *
 * 2016-2-19下午11:03:44
 */
public class OtherActivity extends Activity {

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		
		super.onCreate(savedInstanceState);
		setContentView(R.layout.other);
	}

	
}


ManiActivity

/**
 * 发送通知
	
 * @author 刘楠
 *
 * 2016-2-19下午7:20:05
 */
public class MainActivity extends Activity {

	private NotificationManager manager;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		//获取通知管理器
		manager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
		
	}


	/**
	 * 发送通知
	 * @param v
	 */
	@SuppressLint("NewApi")
	public void send(View v){
		//创建一个启动的Intent意图
		Intent intent = new Intent();
		intent.setClass(this, OtherActivity.class);
		//打开A description of an Intent and target action to perform with it
		PendingIntent pendingIntent =PendingIntent.getActivity(this, 0, intent, 0);
		
		//建立通知
		Notification notification = new Notification.Builder(this)
		//设置打开通知
		.setAutoCancel(true)
		//状态栏显示的信息
		.setTicker("状态栏显示的信息")
		//设置通知的图标
		.setSmallIcon(R.drawable.notify)
		//设置内容标题
		.setContentTitle("一条新的通知")
		//设置通知的内容
		.setContentText("恭喜您,您加薪了,工资增加20%")
		.setContentIntent(pendingIntent)
		//设置通知的声音
		.build();
		//发送一个通知
		manager.notify(0, notification);
		
		
		
	}

}

 
2.TitleProgressBar

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	android:orientation="horizontal"
	android:layout_width="match_parent"
	android:layout_height="match_parent">
	<Button
		android:id="@+id/btn1"
		android:layout_width="wrap_content"
		android:layout_height="wrap_content"
		android:layout_gravity="center_horizontal"
		android:text="显示"
		/>
	<Button
		android:id="@+id/btn2"
		android:layout_width="wrap_content"
		android:layout_height="wrap_content"
		android:layout_gravity="center_horizontal"
		android:text="隐藏"
		/>
</LinearLayout>


 
 

/**
 * 标题栏上方显示进度条
	
 * @author 刘楠
 *
 * 2016-2-19下午11:12:21
 */
public class MainActivity extends Activity {

	private Button btn1;
	private Button btn2;

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		
		requestWindowFeature(Window.FEATURE_PROGRESS);
		
		//requestWindowFeature(Window.FEATURE_INDETERMINATE_PROGRESS);
		setContentView(R.layout.activity_main);
		
		
		btn1 = (Button) findViewById(R.id.btn1);
		btn2 = (Button) findViewById(R.id.btn2);
		
		btn1.setOnClickListener(new OnClickListener()
		{
			@Override
			public void onClick(View source)
			{
				//显示不带进度的进度条
				setProgressBarIndeterminateVisibility(true);
				//显示带进度的进度条
				setProgressBarVisibility(true);
				//设置进度条的进度
				setProgress(4500);
			}
		});
		btn2.setOnClickListener(new OnClickListener()
		{
			@Override
			public void onClick(View source)
			{
				//隐藏不带进度的进度条
				setProgressBarIndeterminateVisibility(false);
				//隐藏带进度的进度条
				setProgressBarVisibility(false);
			}
		});
	}
}



3.SeekBar--拖动改变图片的透明度

<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
 	android:orientation="vertical"
    tools:context=".MainActivity" >

    <ImageView
        android:id="@+id/iv"
        android:layout_width="match_parent"
        android:layout_height="240dp"
        android:src="@drawable/lijiang"
        android:alpha="1"/>

    <SeekBar
        android:id="@+id/seekbar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:max="255"
        android:progress="255"
        android:thumb="@drawable/ic_launcher" 
        />
    
    
    <TextView 
        android:gravity="center_horizontal"
        android:id="@+id/tv"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"/>
</LinearLayout>



/**
 * SeekBar
 * 拖动SeekBar来改变图片的透明度
	
 * @author 刘楠
 *
 * 2016-2-19下午11:09:43
 */
public class MainActivity extends Activity {
	
	private ImageView iv;
	
	private SeekBar seekbar;
	
	private TextView tv;
	

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		
		iv = (ImageView) findViewById(R.id.iv);
		seekbar = (SeekBar) findViewById(R.id.seekbar);
		tv = (TextView) findViewById(R.id.tv);
		
		
		seekbar.setOnSeekBarChangeListener(new OnSeekBarChangeListener() {
			
			@Override
			public void onStopTrackingTouch(SeekBar seekBar) {
				tv.setText(seekBar.getProgress()+"");
				
			}
			
			@Override
			public void onStartTrackingTouch(SeekBar seekBar) {
				tv.setText(seekBar.getProgress()+"");
				
			}
			
			@Override
			public void onProgressChanged(SeekBar seekBar, int progress,
					boolean fromUser) {
				tv.setText(seekBar.getProgress()+"");
				
				iv.setImageAlpha(progress);
				
			}
		});
	}

}

4.RationBar---使用星星做为进度条,评价是常用
 
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
 	android:orientation="vertical"
    tools:context=".MainActivity" >

    <ImageView
        android:id="@+id/iv"
        android:layout_width="match_parent"
        android:layout_height="240dp"
        android:src="@drawable/lijiang"
        android:alpha="1"/>

    <RatingBar
        android:id="@+id/ratingbar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:max="255"
        android:progress="255"
	    android:numStars="5"
	    android:stepSize="0.5"/>
    
    
    <TextView 
        android:gravity="center_horizontal"
        android:id="@+id/tv"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"/>
</LinearLayout>




/**
 * 使用星星做为进度条
	
 * @author 刘楠
 *
 * 2016-2-19下午11:14:00
 */
public class MainActivity extends Activity {
	private ImageView iv;

	private RatingBar ratingbar;

	private TextView tv;

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		iv = (ImageView) findViewById(R.id.iv);
		ratingbar = (RatingBar) findViewById(R.id.ratingbar);
		
		ratingbar.setOnRatingBarChangeListener(new OnRatingBarChangeListener() {
			
			@SuppressLint("NewApi")
			@Override
			public void onRatingChanged(RatingBar ratingBar, float rating,
					boolean fromUser) {
				
				iv.setImageAlpha((int) (rating*255/5));
				
			}
		});
	}

}


5.选项卡
 

<?xml version="1.0" encoding="utf-8"?>
<TabHost
	xmlns:android="http://schemas.android.com/apk/res/android"
	android:id="@android:id/tabhost"
	android:layout_width="match_parent"
	android:layout_height="match_parent"
	android:layout_weight="1">
	<LinearLayout
		android:layout_width="match_parent"
		android:layout_height="match_parent"
		android:orientation="vertical">
		<TabWidget
			android:id="@android:id/tabs"
			android:layout_width="match_parent"
			android:layout_height="wrap_content"/>
		<FrameLayout
			android:id="@android:id/tabcontent"
			android:layout_width="match_parent"
			android:layout_height="match_parent">
			<!-- 定义第一个标签页的内容 -->
			<LinearLayout
				android:id="@+id/tab01"
				android:orientation="vertical"
				android:layout_width="match_parent"
				android:layout_height="match_parent">
				<TextView
					android:layout_width="wrap_content"
					android:layout_height="wrap_content"
					android:text="女儿国国王 - 2012/12/12"
					android:textSize="11pt" />
				<TextView
					android:layout_width="wrap_content"
					android:layout_height="wrap_content"
					android:text="东海龙女 - 2012/12/18"
					android:textSize="11pt" />
			</LinearLayout>
			<!-- 定义第二个标签页的内容 -->
			<LinearLayout
				android:id="@+id/tab02"
				android:orientation="vertical"
				android:layout_width="match_parent"
				android:layout_height="match_parent">
				<TextView
					android:layout_width="wrap_content"
					android:layout_height="wrap_content"
					android:text="白骨精  - 2012/08/12"
					android:textSize="11pt" />
				<TextView
					android:layout_width="wrap_content"
					android:layout_height="wrap_content"
					android:text="蜘蛛精 - 2012/09/20"
					android:textSize="11pt" />
			</LinearLayout>
			<!-- 定义第三个标签页的内容 -->
			<LinearLayout
				android:id="@+id/tab03"
				android:orientation="vertical"
				android:layout_width="match_parent"
				android:layout_height="match_parent"
				android:textSize="11pt">
				<TextView
					android:layout_width="wrap_content"
					android:layout_height="wrap_content"
					android:text="孙悟空 - 2012/09/19"
					android:textSize="11pt" />
				<TextView
					android:layout_width="wrap_content"
					android:layout_height="wrap_content"
					android:text="猪八戒  - 2012/10/12"
					android:textSize="11pt" />
			</LinearLayout>
		</FrameLayout>
	</LinearLayout>
</TabHost>



/**
 * 使用选项卡,
 * Activity要继承TabActivity
 * 
	
 * @author 刘楠
 *
 * 2016-2-19下午11:16:12
 */
public class MainActivity extends TabActivity
{
	@Override
	public void onCreate(Bundle savedInstanceState)
	{
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		// 获取该Activity里面的TabHost组件
		TabHost tabHost = getTabHost();
		// 创建第一个Tab页
		TabHost.TabSpec tab1 = tabHost.newTabSpec("tab1")
				.setIndicator("已接电话") // 设置标题
				.setContent(R.id.tab01); //设置内容
		// 添加第一个标签页
		tabHost.addTab(tab1);
		TabHost.TabSpec tab2 = tabHost.newTabSpec("tab2")
				// 在标签标题上放置图标
				.setIndicator("呼出电话", getResources()
						.getDrawable(R.drawable.ic_launcher))
				.setContent(R.id.tab02);
		// 添加第二个标签页
		tabHost.addTab(tab2);
		TabHost.TabSpec tab3 = tabHost.newTabSpec("tab3")
				.setIndicator("未接电话")
				.setContent(R.id.tab03);
		// 添加第三个标签页
		tabHost.addTab(tab3);
	}
}

7.ViewFlipper
 

使用ViewFlipper来播放图片 这个控件继承了ViewAnimator
  调用showPrevious显示上一张
  showNext();显示下一张
	startFlipping();开始自动播放
	stopFlipping();停止播放
	设置进入的动画效果与方向
	viewFlipper.setInAnimation(this, android.R.anim.slide_out_right);
	设置切换下一张时的动画效果与方向
		viewFlipper.setOutAnimation(this, android.R.anim.slide_in_left);


<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
	xmlns:android="http://schemas.android.com/apk/res/android"
	android:layout_width="match_parent"
	android:layout_height="match_parent">
	<ViewFlipper
		android:id="@+id/details"
		android:layout_width="match_parent"
		android:layout_height="match_parent"
		android:flipInterval="2000"
		>
		<ImageView
			android:src="@drawable/android"
			android:layout_width="match_parent"
			android:layout_height="wrap_content">
		</ImageView>
	    
		<ImageView
			android:src="@drawable/java"
			android:layout_width="match_parent"
			android:layout_height="wrap_content">
		</ImageView>
		
		<ImageView
			android:src="@drawable/javaee"
			android:layout_width="match_parent"
			android:layout_height="wrap_content">
		</ImageView>
	</ViewFlipper>
	<Button
		android:text="&lt;"
		android:onClick="prev"
		android:layout_width="wrap_content"
		android:layout_height="wrap_content"
		android:layout_alignParentBottom="true"
		android:layout_alignParentLeft="true"/>
	<Button
		android:layout_width="wrap_content"
		android:layout_height="wrap_content"
		android:layout_alignParentBottom="true"
		android:layout_centerInParent="true"
		android:onClick="auto"
		android:text="自动播放"/>
	<Button
		android:text="&gt;"
		android:onClick="next"
		android:layout_width="wrap_content"
		android:layout_height="wrap_content"
		android:layout_alignParentBottom="true"
		android:layout_alignParentRight="true"/>
</RelativeLayout>


/**
 * 使用ViewFlipper来播放图片 这个控件继承了ViewAnimator
 * 
 * 调用showPrevious显示上一张
 * showNext();显示下一张
	startFlipping();开始自动播放
	stopFlipping();停止播放
	设置进入的动画效果与方向
	viewFlipper.setInAnimation(this, android.R.anim.slide_out_right);
	设置切换下一张时的动画效果与方向
		viewFlipper.setOutAnimation(this, android.R.anim.slide_in_left);
 * @author 刘楠
 *
 * 2016-2-19下午11:19:40
 */

public class MainActivity extends Activity {

	
	private ViewFlipper viewFlipper;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		viewFlipper = (ViewFlipper) findViewById(R.id.details);
	}

	

	/*
	 * 上一个
	 */
	public void prev(View v){
		
		viewFlipper.setInAnimation(this, android.R.anim.slide_out_right);
		viewFlipper.setOutAnimation(this, android.R.anim.slide_in_left);
		viewFlipper.showPrevious();
		viewFlipper.stopFlipping();
		
		
	}
	
	/*
	 * 下一个
	 */
	public void next(View v){
		viewFlipper.setInAnimation(this, android.R.anim.slide_in_left);
		viewFlipper.setOutAnimation(this, android.R.anim.slide_out_right);
		viewFlipper.showNext();
		viewFlipper.stopFlipping();
	}
	
	/*
	 * 自动播放
	 */
	public void auto(View v){
		viewFlipper.setInAnimation(this, android.R.anim.slide_in_left);
		viewFlipper.setOutAnimation(this, android.R.anim.slide_out_right);
		viewFlipper.startFlipping();
	}
}


8.TextSwitch
 
使用时要建立一个工厂,返回必须为TextView



<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context=".MainActivity" >

    <TextSwitcher
        android:id="@+id/textSwicher"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:inAnimation="@android:anim/slide_in_left"
        android:outAnimation="@android:anim/slide_out_right"
        android:onClick="next"/>

</LinearLayout>


/**
 * TextSwitcher继承 ViewSwitcher继承ViewAnimator
 * 使用时要建立一个工厂
 * 
	
 * @author 刘楠
 *
 * 2016-2-19下午11:23:23
 */

public class MainActivity extends Activity {

	
	private TextSwitcher textSwicher;
	private String [] strs = {
			"疯狂JAVA讲义",
			"疯狂Andorid讲义",
			"疯狂XML讲义",
			"疯狂Ajax讲义",
			"疯狂JAVA EE讲义"
	};
	private int cusStr=0;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		
		textSwicher = (TextSwitcher) findViewById(R.id.textSwicher);
		
		//设置工厂
		textSwicher.setFactory(new ViewFactory() {
			
			@Override
			public View makeView() {
				TextView tv= new TextView(MainActivity.this);
				tv.setTextSize(40);
				tv.setTextColor(Color.MAGENTA);
				return tv;
			}
		});
	}

	
	public void next(View view){
		
		textSwicher.setText(strs[cusStr++%strs.length]);
		
	}


}


9.Data类控件

CalendarView
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:orientation="vertical"
    tools:context="com.itheima.calendarview.MainActivity">

    <TextView
        android:gravity="center_horizontal"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textSize="28sp"
        android:textColor="#66ff0000"
        android:text="请选择日期"/>

    <CalendarView
        android:id="@+id/cv"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:firstDayOfWeek="1"
        android:shownWeekCount="4"
        android:selectedWeekBackgroundColor="#aff"
        android:focusedMonthDateColor="#f00"
        android:weekSeparatorLineColor="#ff0"
        android:unfocusedMonthDateColor="#f9f">

    </CalendarView>

    <TextView
        android:id="@+id/tv"
        android:gravity="center_horizontal"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textSize="28sp"
        android:textColor="#66ff0000"/>

</LinearLayout>

 


public class MainActivity extends AppCompatActivity {


    private CalendarView cv;
    private TextView tv;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        cv = (CalendarView) findViewById(R.id.cv);
        tv = (TextView) findViewById(R.id.tv);


        //设置监听器
        cv.setOnDateChangeListener(new CalendarView.OnDateChangeListener() {
            @Override
            public void onSelectedDayChange(CalendarView view, int year, int month, int dayOfMonth) {
                //显示用户选择日期
                Toast.makeText(MainActivity.this,year+"年"+month+"月"+dayOfMonth+"日",Toast.LENGTH_SHORT).show();
            }
        });
    }
}

DatePicker与TimePicker

 


<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:orientation="vertical"
    tools:context="com.itheima.datetimepicker.MainActivity">

    <TextView
        android:gravity="center_horizontal"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textSize="28sp"
        android:text="请选择日期"/>


    <DatePicker
        android:id="@+id/dataPicker"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:calendarViewShown="true"
        android:calendarTextColor="@color/colorAccent"
        android:layout_gravity="center_horizontal"
        android:startYear="2000"
        android:endYear="2020"
        android:spinnersShown="true">

    </DatePicker>


    <TimePicker
        android:id="@+id/timePicker"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        >

    </TimePicker>

    <EditText
        android:id="@+id/et_show"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:editable="false"
        android:cursorVisible="false"/>
</LinearLayout>




public class MainActivity extends AppCompatActivity {

    private DatePicker dataPicker;
    private TimePicker timePicker;
    private EditText et_show;
    private int year;
    private int month;
    private int day;
    private int hour;
    private int minute;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        dataPicker = (DatePicker) findViewById(R.id.dataPicker);
        timePicker = (TimePicker) findViewById(R.id.timePicker);
        et_show = (EditText) findViewById(R.id.et_show);

        /*
        获取当前时间
         */
        Calendar calendar = Calendar.getInstance();

        year =calendar.get(Calendar.YEAR);
        month =calendar.get(Calendar.MONTH);
        day =calendar.get(Calendar.DAY_OF_MONTH);
        hour =calendar.get(Calendar.HOUR_OF_DAY);
        minute =calendar.get(Calendar.MINUTE);
        /*
        初始化日期DataPicker组件
         */
        dataPicker.init(year, month, day, new DatePicker.OnDateChangedListener() {
            @Override
            public void onDateChanged(DatePicker view, int year, int monthOfYear, int dayOfMonth) {

                MainActivity.this.year=year;

                MainActivity.this.month = monthOfYear;

                MainActivity.this.day = dayOfMonth;

                showDate(year,month,day,hour,minute);
            }
        });

        /*
        设置监听器
         */
        timePicker.setOnTimeChangedListener(new TimePicker.OnTimeChangedListener() {
            @Override
            public void onTimeChanged(TimePicker view, int hourOfDay, int minute) {
                MainActivity.this.hour=hourOfDay;
                MainActivity.this.minute = minute;

                showDate(year,month,day,hour,minute);
            }
        });


    }



    private void showDate(int year,int month,int day,int hour,int minute){

        et_show.setText("日期:"+year+"年"+month+"月"+day+"月"+hour+"时"+minute+"分");
    }
}



10.SearchView
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:orientation="vertical"
    tools:context="com.itheima.searchview.MainActivity">

    <SearchView
        android:id="@+id/sv"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"/>


    <ListView
        android:id="@+id/lv"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1">

    </ListView>
</LinearLayout>
11. 自动完成文件框atutoCompleteTextView
   


/**
 * 搜索框的使用
 */
public class MainActivity extends AppCompatActivity {

    private SearchView sv;
    private ListView lv;
    private String [] mStrings = {
            "aaa",
            "bbbb",
            "ccccc",
            "dddddd",
            "eeeeeee"
    };
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        sv = (SearchView) findViewById(R.id.sv);
        lv = (ListView) findViewById(R.id.lv);
        //适配器
        lv.setAdapter(new ArrayAdapter<String>(this, android.R.layout.simple_list_item_1, mStrings));

        //启用过滤
        lv.setTextFilterEnabled(true);
        //是否自动缩小为图标
        sv.setIconifiedByDefault(false);
        //显示搜索按键
        sv.setSubmitButtonEnabled(true);
        //设置搜索框内显示的文件
        sv.setQueryHint("查找");

        sv.setOnQueryTextListener(new SearchView.OnQueryTextListener() {
            @Override
            public boolean onQueryTextSubmit(String query) {
                Toast.makeText(MainActivity.this,"选择是:"+query,Toast.LENGTH_SHORT).show();
                return false;

            }


            /**
             * 单击搜索按键时激发的事件
             * @param newText
             * @return
             */
            @Override
            public boolean onQueryTextChange(String newText) {


                if(TextUtils.isEmpty(newText)){
                    //清除listview中的内容
                    lv.clearTextFilter();
                }else{
                    lv.setFilterText(newText);
                }
                return true;
            }
        });
    }
}


11自动完成文件框atutoCompleteTextView
 
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:orientation="vertical"
    tools:context=".MainActivity" >

    <AutoCompleteTextView 
        android:id="@+id/auto"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:completionHint="请选择喜欢的图书"
        android:dropDownHorizontalOffset="10dp"
        android:completionThreshold="1"/>


    <MultiAutoCompleteTextView 
        android:id="@+id/mauto"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:completionThreshold="1"/>
</LinearLayout>

/**
 * 自动完成文件框
	
 * @author 刘楠
 *
 * 2016-2-19下午11:55:16
 */
public class MainActivity extends Activity {

	private AutoCompleteTextView auto;
	private MultiAutoCompleteTextView mauto;
	String[] books = { "疯狂JAVA讲义", "疯狂Android讲义", "疯狂Ajax讲义", "疯狂XML讲义",
			"疯狂workflow讲义" };

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		auto = (AutoCompleteTextView) findViewById(R.id.auto);
		mauto = (MultiAutoCompleteTextView) findViewById(R.id.mauto);

		ArrayAdapter<String> adapter = new ArrayAdapter<String>(
				MainActivity.this, android.R.layout.simple_dropdown_item_1line,
				books);
		
		auto.setAdapter(adapter);
		
		mauto.setAdapter(adapter);
		//设置分隔符
		mauto.setTokenizer(new MultiAutoCompleteTextView.CommaTokenizer());

	}

}

12.时钟
 


<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	android:orientation="vertical"
	android:layout_width="match_parent"
	android:layout_height="match_parent"
	android:gravity="center_horizontal">
	<!-- 定义模拟时钟 -->
	<AnalogClock
		android:layout_width="wrap_content"
		android:layout_height="wrap_content"/>
	<!-- 定义数字时钟 -->
	<TextClock
		android:layout_width="wrap_content"
		android:layout_height="wrap_content"
		android:textSize="10pt"
		android:textColor="#f0f"
		android:format12Hour="yyyy年MM月dd日 H:mma EEEE"
		android:drawableEnd="@drawable/ic_launcher"/>
	<!-- 定义模拟时钟，并使用自定义表盘、时针图片 -->
	<AnalogClock
		android:layout_width="wrap_content"
		android:layout_height="wrap_content"
		android:dial="@drawable/watch"
		android:hand_minute="@drawable/hand"/>
</LinearLayout>

13.计时器
 
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	android:orientation="vertical"
	android:layout_width="match_parent"
	android:layout_height="match_parent"
	android:gravity="center_horizontal">
	<Chronometer
		android:id="@+id/test"
		android:layout_width="wrap_content"
		android:layout_height="wrap_content"
		android:textSize="12pt"
		android:textColor="#ffff0000"/>
	<Button
		android:id="@+id/start"
		android:layout_width="wrap_content"
		android:layout_height="wrap_content"
		android:text="启动"/>
</LinearLayout>




/**
 * 计时器
	
 * @author 刘楠
 *
 * 2016-2-20上午12:15:09
 */
public class MainActivity extends Activity {
	Chronometer ch;
	Button start;
	@Override
	public void onCreate(Bundle savedInstanceState)
	{
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		// 获取计时器组件
		ch = (Chronometer) findViewById(R.id.test);
		// 获取“开始”按钮
		start = (Button) findViewById(R.id.start);
		start.setOnClickListener(new View.OnClickListener()
		{
			@Override
			public void onClick(View source)
			{
				// 设置开始计时时间
				ch.setBase(SystemClock.elapsedRealtime());
				// 启动计时器
				ch.start();
				start.setEnabled(false);
			}
		});
		// 为Chronometer绑定事件监听器
		ch.setOnChronometerTickListener(new OnChronometerTickListener()
		{
			@Override
			public void onChronometerTick(Chronometer ch)
			{
				// 如果从开始计时到现在超过了20s
				if (SystemClock.elapsedRealtime() - ch.getBase() > 20 * 1000)
				{
					ch.stop();
					start.setEnabled(true);
				}
			}
		});
	}

}



