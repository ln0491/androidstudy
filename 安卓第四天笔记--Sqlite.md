# AndroidStudy
Android学习
安卓第四天笔记--Sqlite
1.数据库的创建运行与更新
1.1.创建一个类继承SqliteOpenHelper
  
1.2.创建构造方法
 
/**

 * 数据库创建类

    

 * @author 刘楠

 *

 * 2016-2-20上午10:04:34

 */

public class DbSqliteOpenHelper extends SQLiteOpenHelper {

 

     /**

      *

      * @param context 上下文

      * @param name  数据库的名字如: students.db

      * @param factory  游标工厂 ,类似JDBC中的ResultSet中的hasNext();

      * @param version  数据库的版本

      */

     public DbSqliteOpenHelper(Context context, String name,

              CursorFactory factory, int version) {

         super(context, name, factory, version);

     }

 

     /**

      * 当数据库被创建时,执行,

      * 只会执行一次

      */

     @Override

     public void onCreate(SQLiteDatabase db) {

        

 

     }

 

     /**

      * 当数据版本升级时执行,

      * 数据版本只能升不能降 从1开始

      * 这里常用于更改数据库中的表的结构,如添加列,删除列,之类的,创建新的表

      */

     @Override

     public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {

     }

}
 
 
 
1.3.更改构造方法
 
/**

      *

      * @param context 上下文

      * @param name  数据库的名字如: students.db

      * @param factory  游标工厂 ,类似JDBC中的ResultSet中的hasNext();

      * @param version  数据库的版本

      */

     public DbSqliteOpenHelper(Context context) {

         super(context, "student.db", null, 1);

     }

 

 

更改后

/**

      *

      * @param context 上下文

      * @param name  数据库的名字如: students.db

      * @param factory  游标工厂 ,类似JDBC中的ResultSet中的hasNext();

      * @param version  数据库的版本 这里的版本必须大于0,如果为0就会报错

      */

     public DbSqliteOpenHelper(Context context) {

         super(context, "student.db", null, 1);

     }
 
 
 
源码中的解释,数据库版本必须大于等于0,否则会抛出异常
  
将数据库版本设置为0,运行
  
不能降版本
  
 
1.4.在onCreate方法中创建数据库表
 
/**

      * 当数据库被创建时,执行,

      * 只会执行一次

      */

     @Override

     public void onCreate(SQLiteDatabase db) {

         //创建数据库表

         //sql    create table students(_id integer primary key, name varchar(30))

         db.execSQL("create table students(_id integer primary key, name varchar(30))");

         Log.i(TAG, "onCreate 方法执行了");

 

     }
 
 
 
1.5.在Activity中实例化
 
 
 
/**

 * 数据库初始化

    

 * @author 刘楠

 *

 * 2016-2-20上午10:27:04

 */

public class MainActivity extends Activity {

 

     private static final String TAG = "MainActivity";

     private DbSqliteOpenHelper helper;

     @Override

     protected void onCreate(Bundle savedInstanceState) {

         super.onCreate(savedInstanceState);

         setContentView(R.layout.activity_main);

         //实例化

         helper = new DbSqliteOpenHelper(this);

        

         Log.i(TAG, "helper 实例化");

         //执行下方的代码后才会创建数据库

         helper.getWritableDatabase();

     }

 

}
 
 
 
创建数据库后并不会被创建,只有执行了,getReadDatabase或者getWriteDatabase(),
才会执行onCreate中的方法
 
  
运行查看LOG
  
 
 
1.6.数据已经创建,查看数据库
 
将数据库文件导出到桌面
SQLite Expert Professional 3打开
  
 
 
1.7.修改数据库版本,同是添加一列
 
 
/**

      * 当数据版本升级时执行,

      * 数据版本只能升不能降 从1开始

      * 这里常用于更改数据库中的表的结构,如添加列,删除列,之类的,创建新的表

      */

     @Override

     public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {

         Log.i(TAG, "onUpgrade 方法执行了");

        

         //sql alter table  students add gender varchar(10)

        

        

              //添加列

              db.execSQL("alter table  students add gender varchar(10)");

 

     }

 

 
 
 
 
 
查看日志
  
1.8.导出数据库中的文件再次查看
  
 
列已经添加成功
2.传统方式操作数据库
使用sql来操作数据库
 
/**

 * 传统数据库操作工具类

      

 * @author 刘楠

 *

 * 2016-2-20下午12:19:16

 */

public class StudentDao {

      

       private static final String TAG = "StudentDao";

       /*

        * 操作数据库类

        */

       private DbSqliteOpenHelper helper;

      

       public StudentDao(Context context){

              helper = new DbSqliteOpenHelper(context);

       }

      

       /**

        * 插入方法

        * @param name 姓名

        * @param gender 性别

        * @param stuno 学号

        */

       public void insert(String name,String gender,String stuno){

              //获取数据

              SQLiteDatabase db = helper.getWritableDatabase();

             

              String sql="insert into students values (null,?,?,?)";

              //填充占位符

              Object [] bindArgs={name,gender,stuno};

              //执行SQL

              db.execSQL(sql, bindArgs);

              //关闭数据库连接

              db.close();

       }

       /**

        * 修改更改 update

        * @param name 姓名

        * @param gender 性别

        * @param stuno 学号

        */

       public void update(String name,String gender,String stuno){

              //获取数据

              SQLiteDatabase db = helper.getWritableDatabase();

             

              String sql="update students set gender=?,stuno=? where name=?";

              //填充占位符

              Object [] bindArgs={gender,stuno,name};

              //执行SQL

              db.execSQL(sql, bindArgs);

              //关闭数据库连接

              db.close();

       }

       /**

        * 删除

        * @param name 姓名

        */

       public void delete(String name){

              //获取数据

              SQLiteDatabase db = helper.getWritableDatabase();

             

              String sql="delete from  students where name=?";

              //填充占位符

              Object [] bindArgs={name};

              //执行SQL

              db.execSQL(sql, bindArgs);

              //关闭数据库连接

              db.close();

       }

       /**

        * 查询全部

        *

        */

       public void getAll(){

              //获取数据

              SQLiteDatabase db = helper.getReadableDatabase();

             

              String sql="select _id,name,gender,stuno from students";

 

              //执行SQL

              Cursor cursor = db.rawQuery(sql, null);

             

              while(cursor.moveToNext()){

                     int _id = cursor.getInt(cursor.getColumnIndex("_id"));

                     String name = cursor.getString(cursor.getColumnIndex("name"));

                     String gender = cursor.getString(cursor.getColumnIndex("gender"));

                     String stuno = cursor.getString(cursor.getColumnIndex("stuno"));

                    

                     Log.i(TAG, "_id:"+_id+"name:"+name+",gender:"+gender+",stuno:"+stuno);

              }

              cursor.close();

              //关闭数据库连接

              db.close();

       }

       /**

        * 查询全部

        *

        */

       public void getOne(String stuname){

              //获取数据

              SQLiteDatabase db = helper.getReadableDatabase();

             

              String sql="select _id,name,gender,stuno from students where name=?";

               String[] selectionArgs = {stuname};

              //执行SQL

              Cursor cursor = db.rawQuery(sql, selectionArgs);

             

              while(cursor.moveToNext()){

                     int _id = cursor.getInt(cursor.getColumnIndex("_id"));

                     String name = cursor.getString(cursor.getColumnIndex("name"));

                     String gender = cursor.getString(cursor.getColumnIndex("gender"));

                     String stuno = cursor.getString(cursor.getColumnIndex("stuno"));

                    

                     Log.i(TAG, "_id:"+_id+"name:"+name+",gender:"+gender+",stuno:"+stuno);

              }

              cursor.close();

              //关闭数据库连接

              db.close();

       }

}
 
 
 
测试类
 
/**

 * 测试传统方式数据库工具类

      

 * @author 刘楠

 *

 * 2016-2-20下午12:48:37

 */

public class TestStudentDao extends  AndroidTestCase{

 

       private  StudentDao studentDao;

      

       @Override

       protected void setUp() throws Exception {

              studentDao = new StudentDao(getContext());

       }

       /**

        * 测试插入

        */

       public void testInsert(){

              studentDao.insert("lisi", "man", "heimaAndroid2179");

       }

       /**

        * 测试插入

        */

       public void testDelete(){

              studentDao.delete("liunan");

             

       }

       /**

        * 测试修改

        */

       public void testUpdate(){

              studentDao.update("lisi", "女", "33");

       }

       /**

        * 测试查询全部

        */

       public void testGetAll(){

              studentDao.getAll();

             

       }

       /**

        * 测试查询一个

        */

       public void testGetOne(){

              studentDao.getOne("lisi");

             

       }

}

 
 
 
 
3.数据版本判断与更新
 
/**

 * 数据库创建类

      

 * @author 刘楠

 *

 * 2016-2-20上午10:04:34

 */

public class DbSqliteOpenHelper extends SQLiteOpenHelper {

 

       private static final String TAG = "DbSqliteOpenHelper";

 

       /**

        *

        * @param context 上下文

        * @param name  数据库的名字如: students.db

        * @param factory  游标工厂 ,类似JDBC中的ResultSet中的hasNext();

        * @param version  数据库的版本 这里的版本必须大于0,如果为0就会报错

        */

       public DbSqliteOpenHelper(Context context) {

              super(context, "student.db", null,4);

       }

 

       /**

        * 当数据库被创建时,执行,

        * 只会执行一次

        * 第二闪运行是就不会执行了

        */

       @Override

       public void onCreate(SQLiteDatabase db) {

              int version = db.getVersion();

              Log.i(TAG, "version:"+version);

              Log.i(TAG, "onCreate 方法执行了");

              if(version==0){

                     db.execSQL("create table students(_id integer primary key autoincrement, name varchar(30),gender varchar(10),stuno varchar(50))");

                     Log.i(TAG, "version=0执行了");

              }else{

                     //创建数据库表

                     //sql    create table students(_id integer primary key, name varchar(30))

                     db.execSQL("create table students(_id integer primary key autoincrement, name varchar(30))");

                     Log.i(TAG, "version不等0执行了");

              }

                    

             

       }

 

       /**

        * 当数据版本升级时执行,

        * 数据版本只能升不能降 从1开始

        * 这里常用于更改数据库中的表的结构,如添加列,删除列,之类的,创建新的表

        */

       @Override

       public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {

              Log.i(TAG, "onUpgrade 方法执行了");

              Log.i(TAG, oldVersion+"");

              Log.i(TAG, newVersion+"");

 

             

              //算出新 旧版本的差

              int difference = newVersion-oldVersion;

             

              switch (difference) {

              case 1:

                     db.execSQL("alter table  students add gender varchar(10)");

                     Log.i(TAG, difference+"");

                     break;

              case 2:

                     db.execSQL("alter table  students add gender varchar(10))");

                     db.execSQL("alter table  students add stuno varchar(50))");

                     Log.i(TAG, difference+"");

                     break;

 

              default:

                     break;

              }

             

             

       }

 

}
 
 
 
 
4.使用Android提供的API来操作数据库
 
/**

 * 使用Android中的API来执行操作数据库

 *

 * @author 刘楠

 *

 *         2016-2-20下午12:19:16

 */

public class StudentDao2 {

 

       private static final String TAG = "StudentDao2";

       /*

        * 操作数据库类

        */

       private DbSqliteOpenHelper helper;

 

       public StudentDao2(Context context) {

              helper = new DbSqliteOpenHelper(context);

       }

 

       /**

        * 插入方法

        *

        * @param name

        *            姓名

        * @param gender

        *            性别

        * @param stuno

        *            学号

        */

       public long insert(String name, String gender, String stuno) {

              // 获取数据

              SQLiteDatabase db = helper.getWritableDatabase();

 

              // 填充占位符

              ContentValues values = new ContentValues();

              values.put("name", name);

              values.put("gender", gender);

              values.put("stuno", stuno);

 

              String nullColumnHack = "values(null,?,?,?)";

              // 执行SQL

              long insert = db.insert("students", nullColumnHack, values);

              // 关闭数据库连接

              db.close();

              return insert;

       }

 

       /**

        * 修改更改 update

        *

        * @param name

        *            姓名

        * @param gender

        *            性别

        * @param stuno

        *            学号

        */

       public int update(String name, String gender, String stuno) {

              // 获取数据

              SQLiteDatabase db = helper.getWritableDatabase();

 

              String sql = "update students set gender=?,stuno=? where name=?";

      

              // 填充占位符

              ContentValues values = new ContentValues();

             

              values.put("gender", gender);

              values.put("stuno", stuno);   

              // 执行SQL

              int update = db.update("students", values, " name=?", new String[]{name});

              // 关闭数据库连接

              db.close();

              return update;

       }

 

       /**

        * 删除

        *

        * @param name

        *            姓名

        */

       public int delete(String name) {

              // 获取数据

              SQLiteDatabase db = helper.getWritableDatabase();

 

              // 执行SQL

              int delete = db.delete("students", " name=?", new String[]{name});

              // 关闭数据库连接

              db.close();

              return delete;

       }

 

       /**

        * 查询全部

        *

        */

       public void getAll() {

              // 获取数据

              SQLiteDatabase db = helper.getReadableDatabase();

 

              String sql = "select _id,name,gender,stuno from students";

 

              // 执行SQL

              Cursor cursor = db.query("students", new String[]{"_id","name","gender","stuno"}, null, null, null, null, null);

 

              while (cursor.moveToNext()) {

                     int _id = cursor.getInt(cursor.getColumnIndex("_id"));

                     String name = cursor.getString(cursor.getColumnIndex("name"));

                     String gender = cursor.getString(cursor.getColumnIndex("gender"));

                     String stuno = cursor.getString(cursor.getColumnIndex("stuno"));

 

                     Log.i(TAG, "_id:" + _id + "name:" + name + ",gender:" + gender

                                   + ",stuno:" + stuno);

              }

              cursor.close();

              // 关闭数据库连接

              db.close();

       }

 

       /**

        * 查询全部

        *

        */

       public void getOne(String stuname) {

              // 获取数据

              SQLiteDatabase db = helper.getReadableDatabase();

              // 执行SQL

              Cursor cursor = db.query("students", new String[]{"_id","name","gender","stuno"}, "name =?", new String[]{stuname}, null, null, null);

 

              while (cursor.moveToNext()) {

                     int _id = cursor.getInt(cursor.getColumnIndex("_id"));

                     String name = cursor.getString(cursor.getColumnIndex("name"));

                     String gender = cursor.getString(cursor.getColumnIndex("gender"));

                     String stuno = cursor.getString(cursor.getColumnIndex("stuno"));

 

                     Log.i(TAG, "_id:" + _id + "name:" + name + ",gender:" + gender

                                   + ",stuno:" + stuno);

              }

              cursor.close();

              // 关闭数据库连接

              db.close();

       }

}

 
 
 
 
 
测试
 
 
 
/**

 * 测试传统方式数据库工具类

      

 * @author 刘楠

 *

 * 2016-2-20下午12:48:37

 */

public class TestStudentDao2 extends  AndroidTestCase{

 

       private static final String TAG = "TestStudentDao2";

       private  StudentDao2 studentDao2;

      

       @Override

       protected void setUp() throws Exception {

              studentDao2 = new StudentDao2(getContext());

       }

       /**

        * 测试插入

        */

       public void testInsert(){

              long result = studentDao2.insert("lisi", "man", "heimaAndroid2179");

              Log.i(TAG, result+"");

       }

       /**

        * 测试插入

        */

       public void testDelete(){

              int delete = studentDao2.delete("aa");

              Log.i(TAG, delete+"");

             

       }

       /**

        * 测试修改

        */

       public void testUpdate(){

              int update = studentDao2.update("lisi", "hh", "33");

              Log.i(TAG, update+"");

       }

       /**

        * 测试查询全部

        */

       public void testGetAll(){

              studentDao2.getAll();

             

       }

       /**

        * 测试查询一个

        */

       public void testGetOne(){

              studentDao2.getOne("gh");

             

       }

}

 
 
 
 
