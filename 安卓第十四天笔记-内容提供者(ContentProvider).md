# AndroidStudy
Android学习
#ContentProvider--内容提供者
##1.ContentProvider简介
ContentProvider是不同应用程序之间进行数据交换的一种方式或者称标准API.

* ContentProvider以某种Uri的方式对外提供数据,允许其他应用访问或者修改数据
* 其它的程序根据Uri去访问操作指定的数据


##2.Uri简介
http://www.qq.com:80/news.jsp

* http://:URL的协议部分,只要通过HTTP协议来访问网络,这具部分是固定的.如FTP
* www.qq.com:域名部分
* news.jsp:网站资源部分,访问不能的资源这具部分是不同的,这个部分是动态的

> ContentProvider要求的;Uri与上面类似


        content://com.pc.pp.provider/words
* content://这个Android的ContentProvider规定的,就像默认的http://,ContentProvider的协议默认就是Content://
* com.pc.pp.provider:这部分就是ContentProvider的authorities.系统就是由这具部分来找到操作哪个ContentProvider的.只要访问指定的ContentProvider,这个部分是固定的
* words:资源部分或者数据

##3.建立ContentProvider

1. 写一个类继承ContentProvider重写query,insert,update,delete,getType等方法
2. 在AndroidManifest.xml中注册ContentProvider,指定
          
          android:authorities属性  //这个给其它应用程序使用的


>建立一个数据帮助类
    
    public class BankSqliteOpenHelper extends SQLiteOpenHelper {
    
    	public BankSqliteOpenHelper(Context context) {
    		super(context, "bank.db", null, 1);
    	}
    
    	@Override
    	public void onCreate(SQLiteDatabase db) {
    		String sql = "create table account(_id integer primary key, name varchar(30),money double)";
    		db.execSQL(sql);
    
    	}
    
    	@Override
    	public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
    		
    
    	}
    
    }
>建立一个ContentProvider
    
        /**
 * 本地数据库的内容提供者,
	
 * @author 刘楠
 *
 * 2016-3-2下午8:35:19
 */
    public class BankDbProvider extends ContentProvider {

    	private static final String TAG = "BankDbProvider";
    
    	private static final int ACCOUNT = 0;
    
    	private static final int ACCOUNT_ID = 1;
    	//默认匹配,不能匹配就执行UriMatcher.NO_MATCH
    	private static UriMatcher matcher = new UriMatcher(UriMatcher.NO_MATCH);
    	//数据库操作类
    	private BankSqliteOpenHelper helper;
    	
    	/**
    	 * 初始始化一些可以匹配的URI
    	 */
    	static {
    
    		matcher.addURI("com.itheima.bankdb.BankDbProvider", "account", ACCOUNT);
    		matcher.addURI("com.itheima.bankdb.BankDbProvider", "account/#",
    				ACCOUNT_ID);
    
    	}
    
    	/**
    	 * 这个Provider被创建时执行,做一些初始始化操作
    	 */
    	@Override
    	public boolean onCreate() {
    		
    		helper = new BankSqliteOpenHelper(getContext());
    		return true;
    	}
    
    	/**
    	 * 查询操作
    	 */
    	@Override
    	public Cursor query(Uri uri, String[] projection, String selection,
    			String[] selectionArgs, String sortOrder) {
    		
    		if (matcher.match(uri) == ACCOUNT) {
    
    			SQLiteDatabase db = helper.getReadableDatabase();
    			Cursor cursor = db.query("account", projection, selection,
    					selectionArgs, null, null, sortOrder);
    			return cursor;
    		} else if (matcher.match(uri) == ACCOUNT_ID) {
    
    			SQLiteDatabase db = helper.getReadableDatabase();
    			long id = ContentUris.parseId(uri);
    			Cursor cursor = db.query("account", null, "_id=?",
    					new String[] { id + "" }, null, null, sortOrder);
    			return cursor;
    		} 
    		return null;
    	}
    
    	/**
    	 * 返回类型,主要针对查询操作
    	 * vnd.android.cursor.dir:表示返回多条数据
    	 * vnd.android.cursor.item:表示返回一条数据
    	 */
    	@Override
    	public String getType(Uri uri) {
    		
    		if (matcher.match(uri) == ACCOUNT) {
    			return "vnd.android.cursor.dir/account";
    		} else if (matcher.match(uri) == ACCOUNT_ID) {
    
    			return "vnd.android.cursor.item/account";
    		}
    		
    		return null;
    	}
    
    	/**
    	 * 插入操作
    	 */
    	@Override
    	public Uri insert(Uri uri, ContentValues values) {
    		
    		if (matcher.match(uri) == ACCOUNT) {
    			
    			SQLiteDatabase db = helper.getWritableDatabase();
    			long insert = db.insert("account", null, values);
    
    			db.close();
    		}
    		return uri;
    
    	}
    
    	/**
    	 * 删除操作
    	 */
    	@Override
    	public int delete(Uri uri, String selection, String[] selectionArgs) {
    	
    		if (matcher.match(uri) == ACCOUNT) {
    			
    			SQLiteDatabase db = helper.getWritableDatabase();
    			
    			int delete = db.delete("account", selection, selectionArgs);
    
    			db.close();
    			return delete;
    		}
    		return 0;
    	}
    
    	/**
    	 * 更新操作
    	 */
    	@Override
    	public int update(Uri uri, ContentValues values, String selection,
    			String[] selectionArgs) {
    		
    		if (matcher.match(uri) == ACCOUNT) {
    		
    			SQLiteDatabase db = helper.getWritableDatabase();
    			int update = db.update("account", values, selection, selectionArgs);
    			db.close();
    			return update;
    		}
    		return 0;
    	   }
    
         }

>在清单文件中注册这个provider

    <!-- 注册Provider 
        authorities:对外暴露的访问路径
        -->
        <provider
            android:name="com.itheima.bankdb.provider.BankDbProvider"
            android:authorities="com.itheima.bankdb.BankDbProvider" >
        </provider>

authorities :可以随便定义,建立使用包名.Provider


##4.访问这个应用程序的Provider
>新建一个应用 

    ContentResolver resolver = getContentResolver();

使用这个对象来实现对象Privoider的解析

        /**
	 * 插入数据
	 * 
	 * @param v
	 */
	public void insert(View v) {
		ContentResolver resolver = getContentResolver();

		Uri uri = Uri.parse("content://com.itheima.bankdb.BankDbProvider/account");
		ContentValues values = new ContentValues();
		values.put("name", "lisi");
		values.put("money", 503.3);
		Uri insert = resolver.insert(uri, values);
		System.out.println("insert:"+insert);
	}

	/**
	 * 更新
	 * 
	 * @param v
	 */
	public void update(View v) {
		ContentResolver resolver = getContentResolver();

		Uri uri = Uri.parse("content://com.itheima.bankdb.BankDbProvider/account");
		ContentValues values = new ContentValues();
		values.put("money", 1003.3);
		
		int update = resolver.update(uri, values, "name=?", new String[]{"lisi"});
		System.out.println("update"+update);
	}

	/**
	 * 删除
	 * 
	 * @param v
	 */
	public void delete(View v) {
		ContentResolver resolver = getContentResolver();

		Uri uri = Uri.parse("content://com.itheima.bankdb.BankDbProvider/account");
		
		int delete = resolver.delete(uri, "name=?", new String[]{"lisi"});
		System.out.println("delete:"+delete);
	}

	/**
	 * 查询全部
	 * 
	 * @param v
	 */
	public void query(View v) {
		ContentResolver resolver = getContentResolver();

		Uri uri = Uri.parse("content://com.itheima.bankdb.BankDbProvider/account");
		Cursor cursor = resolver.query(uri, null, null, null, null);
		
		while(cursor.moveToNext()){
			int id=cursor.getInt(cursor.getColumnIndex("_id"));
			String name = cursor.getString(cursor.getColumnIndex("name"));
			
			double money =cursor.getDouble(cursor.getColumnIndex("money"));
			Toast.makeText(this, "ID:"+id+"姓名:"+name+",Money:"+money, Toast.LENGTH_SHORT).show();
			
		}
		cursor.close();
	}

	/**
	 * 查询一个
	 * 
	 * @param v
	 */
	public void queryOne(View v) {
		ContentResolver resolver = getContentResolver();

		Uri uri = Uri.parse("content://com.itheima.bankdb.BankDbProvider/account/2");
		Cursor cursor = resolver.query(uri, null, null, null, null);
		
		while(cursor.moveToNext()){
			int id=cursor.getInt(cursor.getColumnIndex("_id"));
			String name = cursor.getString(cursor.getColumnIndex("name"));
			
			double money =cursor.getDouble(cursor.getColumnIndex("money"));
			Toast.makeText(this, "ID:"+id+"姓名:"+name+",Money:"+money, Toast.LENGTH_SHORT).show();
			
		}
		cursor.close();
	}


##5.使用短信的内容提供者

>向短信中添加一条数据

        /**
	 * 像短信中添加一条数据
	 * @param v
	 */
	public void insert(View v){
		//获取内容解析都
		ContentResolver resolver = getContentResolver();
		//查看源码中找到提供的uri
		Uri uri =Uri.parse("content://sms");
		//要添加的数据就是一个MAP集合,对应的数据表的字段
		ContentValues values = new ContentValues();
		values.put("address", "10086");
		values.put("date", System.currentTimeMillis());
		values.put("body", "这是一条新的短信");
		values.put("type", 1);
		resolver.insert(uri, values);
		
	}
>添加权限 

        <uses-permission android:name="android.permission.READ_SMS"/>
        <uses-permission android:name="android.permission.WRITE_SMS"/>

##6.使用联系人ContentProvider

>查询联系人

        /**
	 * 查询所有联系人,
	 * @param context
	 * @return
	 */
	public static List<ContactInfo>  getContacts(Context context){
		List<ContactInfo> list = new ArrayList<ContactInfo>();
		Uri rowUri = Uri.parse("content://com.android.contacts/raw_contacts");
		Uri dataUri = Uri.parse("content://com.android.contacts/data");
		
		ContentResolver resolver = context.getContentResolver();
		
		Cursor rawCursor = resolver.query(rowUri, new String[]{"contact_id"}, null, null, null);
		while(rawCursor.moveToNext()){
			String id =rawCursor.getString(rawCursor.getColumnIndex("contact_id"));
			if(id==null){
				continue;
			}
			ContactInfo info = new ContactInfo();
			info.id=id;
			Cursor dataCursor= resolver.query(dataUri, new String[]{"data1","mimetype"}, "raw_contact_id=?", new String[]{id}, null);
			while(dataCursor.moveToNext()){
				String data1=dataCursor.getString(dataCursor.getColumnIndex("data1"));
				String mimetype=dataCursor.getString(dataCursor.getColumnIndex("mimetype"));
				
				if("vnd.android.cursor.item/email_v2".equals(mimetype)){
					info.email=data1;
				}else if("vnd.android.cursor.item/im".equals(mimetype)){
					info.im=data1;
				}else if("vnd.android.cursor.item/phone_v2".equals(mimetype)){
					info.phone=data1;
				}else if("vnd.android.cursor.item/name".equals(mimetype)){
					info.name=data1;
				}
			}
			dataCursor.close();
			list.add(info);
		}
		rawCursor.close();
		return list;
	}

>添加联系人

        /**
	 * 添加联系人
	 * 
	 * @param v
	 */
	public void insert(View v) {
		String name = etName.getText().toString().trim();
		String phone = etPhone.getText().toString().trim();
		String email = etEmail.getText().toString().trim();

		if (TextUtils.isEmpty(name) || TextUtils.isEmpty(phone)
				|| TextUtils.isEmpty(email)) {
			Toast.makeText(this, "名字,电话,邮件不能为空", Toast.LENGTH_SHORT).show();
			return;
		}

		Uri rowUri = Uri.parse("content://com.android.contacts/raw_contacts");
		Uri dataUri = Uri.parse("content://com.android.contacts/data");
		// 先查询ID
		ContentResolver resolver = getContentResolver();

		Cursor rawCursor = resolver.query(rowUri, new String[] { "_id" }, null,
				null, null);
		rawCursor.moveToLast();
		int id = 1;
		id = rawCursor.getInt(rawCursor.getColumnIndex("_id")) + 1;
		rawCursor.close();
		// 插入raw_contact表ID
		ContentValues values = new ContentValues();
		values.put("contact_id", id);
		resolver.insert(rowUri, values);
		// 向data表中插入数据
		ContentValues nameValues = new ContentValues();
		nameValues.put("data1", name);
		nameValues.put("mimetype", "vnd.android.cursor.item/name");
		nameValues.put("raw_contact_id", id);
		resolver.insert(dataUri, nameValues);

		ContentValues phoneValues = new ContentValues();
		phoneValues.put("data1", phone);
		phoneValues.put("mimetype", "vnd.android.cursor.item/phone_v2");
		phoneValues.put("raw_contact_id", id);
		resolver.insert(dataUri, phoneValues);

		ContentValues emailValues = new ContentValues();
		emailValues.put("data1", email);
		emailValues.put("mimetype", "vnd.android.cursor.item/email_v2");
		emailValues.put("raw_contact_id", id);
		resolver.insert(dataUri, emailValues);

		Toast.makeText(this, "添加成功", Toast.LENGTH_SHORT).show();
	}



>添加权限

    <uses-permission android:name="android.permission.READ_CONTACTS"/>
    <uses-permission android:name="android.permission.WRITE_CONTACTS"/>    


##7.内容观察者

>ContentObserver 观察某一个路径的数据， 某个指定的数据，一旦数据发生了变化，那么将会收到通知


> 作用： 数据库的数据发生变化之后，会发布一个通知出来，我们收到通知之后，再去查询数据库，即可得到最新的数据


	发布通知

		//参数一： 具体是什么位置的数据发生了改变
			//参数二：具体的内容观察者对象， 如果是null , 那么表明不告诉具体的某一个人，
			getContext().getContentResolver().notifyChange(uri, null);

	收到通知


		//注册内容观察者
		
		Uri uri = Uri.parse("content://com.itheima.bank.BACKDOOR/account");
		//参数一： 观察的具体位置 ，uri
		//参数二： true : uri只要匹配前半段，也能收到通知， false . 所有的字母都全部匹配才能收到通知
		getContentResolver().registerContentObserver(uri, true, new MyObserver(new Handler()));

		----------------------

		class MyObserver extends ContentObserver{

			public MyObserver(Handler handler) {
				super(handler);
			}
			
			@Override
			public void onChange(boolean selfChange) {
				super.onChange(selfChange);
				Log.d(TAG, "表数据发生了改变..");
			}
		}
