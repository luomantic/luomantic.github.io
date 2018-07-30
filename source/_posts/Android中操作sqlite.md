---
title: Android中对数据库的操作
date: 2018/7/20 星期五 10:15:07 
tags: hexo
---

### Ⅰ。Android中的数据库处理
	
	什么情况下我们才用数据库做数据存储？ 大量数据结构相同的数据需要存储时。
	mysql sqlserver2000  sqlite 嵌入式 轻量级

	SqliteOpenHelper

	创建数据库步骤：
	1.创建一个类集成SqliteOpenHelper，需要添加一个构造方法，实现两个方法oncreate ,onupgrade
		构造方法中的参数介绍：

		//context :上下文   ， name：数据库文件的名称    factory：用来创建cursor对象，默认为null 
		//version:数据库的版本号，从1开始，如果发生改变，onUpgrade方法将会调用,4.0之后只能升不能将
		super(context, "info.db", null,1);
		

	2.创建这个帮助类的一个对象，调用getReadableDatabase()方法，会帮助我们创建打开一个数据库

	3.复写oncreate和onupgrdate方法：
		oncreate方法是数据库第一次创建的时候会被调用;  特别适合做表结构的初始化,需要执行sql语句；SQLiteDatabase db可以用来执行sql语句
		
		//onUpgrade数据库版本号发生改变时才会执行； 特别适合做表结构的修改



	帮助类对象中的getWritableDatabase 和 getReadableDatabase都可以帮助我们获取一个数据库操作对象SqliteDatabase.

	区别：
	getReadableDatabase:
		先尝试以读写方式打开数据库，如果磁盘空间满了，他会重新尝试以只读方式打开数据库。
	getWritableDatabase:
		直接以读写方式打开数据库，如果磁盘空间满了，就直接报错。
		
### Ⅱ。Android下数据库第一种方式增删改查


	1.创建一个帮助类的对象，调用getReadableDatabase方法，返回一个SqliteDatebase对象

	2.使用SqliteDatebase对象调用execSql()做增删改,调用rawQuery方法做查询。

	******特点:增删改没有返回值，不能判断sql语句是否执行成功。sql语句手动写，容易写错



	private MySqliteOpenHelper mySqliteOpenHelper;
	public InfoDao(Context context){
		//创建一个帮助类对象
		mySqliteOpenHelper = new MySqliteOpenHelper(context);

		
	}

	public void add(InfoBean bean){

		//执行sql语句需要sqliteDatabase对象
		//调用getReadableDatabase方法,来初始化数据库的创建
		SQLiteDatabase 	db = mySqliteOpenHelper.getReadableDatabase();
		//sql:sql语句，  bindArgs：sql语句中占位符的值
		db.execSQL("insert into info(name,phone) values(?,?);", new Object[]{bean.name,bean.phone});
		//关闭数据库对象
		db.close();
	}

	public void del(String name){


		//执行sql语句需要sqliteDatabase对象
		//调用getReadableDatabase方法,来初始化数据库的创建
		SQLiteDatabase db = mySqliteOpenHelper.getReadableDatabase();
		//sql:sql语句，  bindArgs：sql语句中占位符的值
		db.execSQL("delete from info where name=?;", new Object[]{name});
		//关闭数据库对象
		db.close();

	}
	public void update(InfoBean bean){

		//执行sql语句需要sqliteDatabase对象
		//调用getReadableDatabase方法,来初始化数据库的创建
		SQLiteDatabase db = mySqliteOpenHelper.getReadableDatabase();
		//sql:sql语句，  bindArgs：sql语句中占位符的值
		db.execSQL("update info set phone=? where name=?;", new Object[]{bean.phone,bean.name});
		//关闭数据库对象
		db.close();

	}
	public void query(String name){
		
		//执行sql语句需要sqliteDatabase对象
		//调用getReadableDatabase方法,来初始化数据库的创建
		SQLiteDatabase db = mySqliteOpenHelper.getReadableDatabase();
		//sql:sql语句，  selectionArgs:查询条件占位符的值,返回一个cursor对象
		Cursor cursor = db.rawQuery("select _id, name,phone from info where name = ?", new String []{name});
		//解析Cursor中的数据
		if(cursor != null && cursor.getCount() >0){//判断cursor中是否存在数据
			
			//循环遍历结果集，获取每一行的内容
			while(cursor.moveToNext()){//条件，游标能否定位到下一行
				//获取数据
				int id = cursor.getInt(0);
				String name_str = cursor.getString(1);
				String phone = cursor.getString(2);
				System.out.println("_id:"+id+";name:"+name_str+";phone:"+phone);
			}
			cursor.close();//关闭结果集
			
		}
		//关闭数据库对象
		db.close();

	}
	
### Ⅲ。Android下另外一种增删改查方式 
	
	1.创建一个帮助类的对象，调用getReadableDatabase方法，返回一个SqliteDatebase对象

	2.使用SqliteDatebase对象调用insert,update,delete ,query方法做增删改查。

	******特点:增删改有了返回值，可以判断sql语句是否执行成功，但是查询不够灵活，不能做多表查询。所以在公司一般人增删改喜欢用第二种方式，查询用第一种方式。

			private MySqliteOpenHelper mySqliteOpenHelper;
	public InfoDao(Context context){
		//创建一个帮助类对象
		mySqliteOpenHelper = new MySqliteOpenHelper(context);
	}

	public boolean add(InfoBean bean){

		//执行sql语句需要sqliteDatabase对象
		//调用getReadableDatabase方法,来初始化数据库的创建
		SQLiteDatabase 	db = mySqliteOpenHelper.getReadableDatabase();
		
		
		ContentValues values = new ContentValues();//是用map封装的对象，用来存放值
		values.put("name", bean.name);
		values.put("phone", bean.phone);
		
		//table: 表名 , nullColumnHack：可以为空，标示添加一个空行, values:数据一行的值 , 返回值：代表添加这个新行的Id ，-1代表添加失败
		long result = db.insert("info", null, values);//底层是在拼装sql语句
	
		//关闭数据库对象
		db.close();
		
		if(result != -1){//-1代表添加失败
			return true;
		}else{
			return false;
		}
	}

	public int del(String name){

		//执行sql语句需要sqliteDatabase对象
		//调用getReadableDatabase方法,来初始化数据库的创建
		SQLiteDatabase db = mySqliteOpenHelper.getReadableDatabase();
		
		//table ：表名, whereClause: 删除条件, whereArgs：条件的占位符的参数 ; 返回值：成功删除多少行
		int result = db.delete("info", "name = ?", new String[]{name});
		//关闭数据库对象
		db.close();
		
		return result;

	}
	public int update(InfoBean bean){

		//执行sql语句需要sqliteDatabase对象
		//调用getReadableDatabase方法,来初始化数据库的创建
		SQLiteDatabase db = mySqliteOpenHelper.getReadableDatabase();
		ContentValues values = new ContentValues();//是用map封装的对象，用来存放值
		values.put("phone", bean.phone);
		//table:表名, values：更新的值, whereClause:更新的条件, whereArgs：更新条件的占位符的值,返回值：成功修改多少行
		int result = db.update("info", values, "name = ?", new String[]{bean.name});
		//关闭数据库对象
		db.close();
		return result;

	}
	public void query(String name){
	
		//执行sql语句需要sqliteDatabase对象
		//调用getReadableDatabase方法,来初始化数据库的创建
		SQLiteDatabase db = mySqliteOpenHelper.getReadableDatabase();
		
		//table:表名, columns：查询的列名,如果null代表查询所有列； selection:查询条件, selectionArgs：条件占位符的参数值,
		//groupBy:按什么字段分组, having:分组的条件, orderBy:按什么字段排序
		Cursor cursor = db.query("info", new String[]{"_id","name","phone"}, "name = ?", new String[]{name}, null, null, "_id desc");
		//解析Cursor中的数据
		if(cursor != null && cursor.getCount() >0){//判断cursor中是否存在数据
			
			//循环遍历结果集，获取每一行的内容
			while(cursor.moveToNext()){//条件，游标能否定位到下一行
				//获取数据
				int id = cursor.getInt(0);
				String name_str = cursor.getString(1);
				String phone = cursor.getString(2);
				System.out.println("_id:"+id+";name:"+name_str+";phone:"+phone);
				
				
			}
			cursor.close();//关闭结果集
			
		}
		//关闭数据库对象
		db.close();

	}

		

### Ⅳ。数据库的事务 
		
	事务： 执行多条sql语句，要么同时执行成功，要么同时执行失败，不能有的成功，有的失败

	银行转账


	//点击按钮执行该方法
	public void transtation(View v){
		//1.创建一个帮助类的对象
		BankOpenHelper bankOpenHelper = new BankOpenHelper(this);
		//2.调用数据库帮助类对象的getReadableDatabase创建数据库，初始化表数据，获取一个SqliteDatabase对象去做转账（sql语句）
		SQLiteDatabase db = bankOpenHelper.getReadableDatabase();
		//3.转账,将李四的钱减200，张三加200
		db.beginTransaction();//开启一个数据库事务
		try {
			db.execSQL("update account set money= money-200 where name=?",new String[]{"李四"});
			int i = 100/0;//模拟一个异常
			db.execSQL("update account set money= money+200 where name=?",new String[]{"张三"});

			db.setTransactionSuccessful();//标记事务中的sql语句全部成功执行
		} finally {
			db.endTransaction();//判断事务的标记是否成功，如果不成功，回滚错误之前执行的sql语句 
		}
	}

### Ⅴ。利用GreenDao进行数据库操作。
	
	GreenDao进行数据库操作，其实质也是对sqlite进行操作。
	1. 配置环境
	------------------- ①添加依赖 -----------------------
		// In your root build.gradle file:
		buildscript {
		    repositories {
		        jcenter()
		        mavenCentral() // add repository
		    }
		    dependencies {
		        classpath 'com.android.tools.build:gradle:2.3.0'
		        classpath 'org.greenrobot:greendao-gradle-plugin:3.2.2' // add plugin
		    }
		}
	-------1-------
		// In your app projects build.gradle file:
		apply plugin: 'com.android.application'
		apply plugin: 'org.greenrobot.greendao' // apply plugin
		-------2-------
		dependencies {
		    compile 'org.greenrobot:greendao:3.2.2' // add library
		}
	-------3-------
	------------------- ②解锁技能 ----------------------
	// In the build.gradle file of your app project:
		android {
		...
		}
		
		greendao {
		    schemaVersion 1
		    daoPackage 'com.ping.greendao.gen'
		    targetGenDir 'src/main/java'
		}
	
	====================================================
	
	2. 在代码中使用：
	------------------- ①初始化数据 ---------------------
	   private static DaoSession daoSession;

	    @Override
	    public void onCreate() {
	        super.onCreate();
	        //配置数据库
	        setupDatabase();
	    }
	
	    /**
	     * 配置数据库
	     */
	    private void setupDatabase() {
	        DaoMaster.DevOpenHelper helper = new DaoMaster.DevOpenHelper(this, "shop.db", null);
	        SQLiteDatabase db = helper.getWritableDatabase();
	        DaoMaster daoMaster = new DaoMaster(db);
	        daoSession = daoMaster.newSession();
	    }
	
	    public static DaoSession getDaoInstant() {
	        return daoSession;
	    }
	    
	------------- ②对操作数据的方法进行封装  --------------
	public class LoveDao {

	    /**
	     * 添加数据
	     */
	    public static void insertLove(Shop shop) {
	        BaseApplication.getDaoInstant().getShopDao().insert(shop);
	    }
	
	    /**
	     * 删除数据
	     */
	    public static void deleteLove(long id) {
	        BaseApplication.getDaoInstant().getShopDao().deleteByKey(id);
	    }
	
	    /**
	     * 更新数据
	     */
	    public static void updateLove(Shop shop) {
	        BaseApplication.getDaoInstant().getShopDao().update(shop);
	    }
	
	    /**
	     * 查询条件为Type=TYPE_LOVE的数据
	     */
	    public static List<Shop> queryLove() {
	        return BaseApplication.getDaoInstant().getShopDao().queryBuilder().where(ShopDao.Properties.Type.eq(Shop.TYPE_LOVE)).list();
	    }
	}
	------------------------------------------
	

### Ⅵ。利用其他数据库，代替sqlite数据库。<略>

