# 016_整合OrmLite_创建数据库和表
## 学习目标
- 熟练使用 OrmLite 创建数据库和数据表基本用法

## 学习基础要求
熟悉 Android 中关系数据库 Sqlite 的使用

## 引言和回顾
在 Android 中使用原始的 SQLiteOpenHelper 操作数据库显得过于繁琐，而且对于不是很熟悉数据库操作的人来说比较容易出现一些隐藏的漏洞。所以一般都会想到使用相关的 ORM 框架完成开发，在提高开发效率的同时，也可以有效避免数据库操作漏洞对应用带来的影响。

到现在为止，Android 中 ORM 框架也已经有很多，比如 OrmLite，Litepal，Sugar，GreenDAO，ActiveAndroid，Realm等等。对于他们之间的对比，各有长短，我们工作中应该选择稳定的和较为熟悉的工具。其中，OrmLite 是使用较为广泛的一个，下面我们就结合当前项目的需求来回顾 OrmLite 的使用步骤，我们先来创建数据表

## 课堂内容

### 1. 在 Module 的 build.gradle 文件中添加 OrmLite 依赖
```java
dependencies {
    ....

    // ormlite
    compile 'com.j256.ormlite:ormlite-android:5.0'
}
```

### 2. 定义数据库表对应的 JavaBean 建立映射关系
```java
@DatabaseTable(tableName = "t_address")
public class AddressBean {
    @DatabaseField(id = true) //id = true, 表示 _id 是主键
    private int _id;
    @DatabaseField(canBeNull = false) //canBeNullb 表示 goodsAddress 在表中可以为空
    private String goodsAddress;
    @DatabaseField(canBeNull = false)
    private String village;

    @DatabaseField(canBeNull = false,foreign = true,foreignColumnName = "_id",columnName = "user_id") //foreign 表示表中 user_id 列关联外键，关联 UserBean 表中的 _id  列
    private UserBean user;

    ......
}
```

1. @DatabaseField(canBeNull = false)，canBeNullb 表示 goodsAddress 在表中可以为空
2. @DatabaseField(canBeNull = false,foreign = true,foreignColumnName = "_id",columnName = "user_id")，foreign 表示表中 user_id 列与 UserBean 表中的 _id  列相关联

```java
@DatabaseTable(tableName = "t_user") //使用 @DatabaseTable 注解标识表名 t_user 表名
public class UserBean {
    @DatabaseField(columnName = "_id",id = true)
    private int _id;

    // 需要有一个集合去装当前用户的所有地址列表信息
    @ForeignCollectionField(eager = true)
    private ForeignCollection<AddressBean> addressList;

    public UserBean() {
    }
    ......
}
```

1. 使用 @DatabaseTable(tableName = "t_user") 注解标识要创建表的 JavaBean，使用 tableName 的值指定表名
2. 使用 @DatabaseField(columnName = "_id", id = true) 注解标识列的数据的值所对应的字段，使用 columnName 的值指定表的列名，使用 id 的值指定是否为 id 字段
3. 使用 @ForeignCollectionField(eager = true) 注解，表示当查询 UserBean 的时候可以查询相关的 AddressBean，eager＝true表示集合中的数据立刻查询出来，否则使用懒加载，在集合被迭代遍历的时候才去数据库查询
4. 注意 ForeignCollection<AddressBean> addressList，地址列表我们使用的是 ForeignCollection 类型的集合，注解使用的是 @ForeignCollectionField
5. User 和 Address 的关系是一对多的关系, 一个用户可以有多个收货地址

### 3. 定义 OrmLiteSqliteOpenHelper 的子类
```java
public class DBHelper extends OrmLiteSqliteOpenHelper{
    private static final String DATABASENAME = "itheima.db";
    private static final int DATABASEVERSION = 1;

    public DBHelper(Context context) {
        //指定数据库名字和数据库版本
        super(context, DATABASENAME, null, DATABASEVERSION);
    }

    @Override
    public void onCreate(SQLiteDatabase database, ConnectionSource connectionSource) {
        try {
            //创建表
            TableUtils.createTable(connectionSource, UserBean.class);
            TableUtils.createTable(connectionSource, AddressBean.class);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void onUpgrade(SQLiteDatabase database, ConnectionSource connectionSource, int oldVersion, int newVersion) {

    }
}
```

1. 继承 OrmLiteSqliteOpenHelper，在Android的开发中，google原版封装了一个 SqliteOpenHelper 供开发者调用；在OrmLite中，对原版的工具进行了加强，提供一个继承自SqliteOpenHelper的OrmLiteSqliteOpenHelper工具类
2. 实现构造方法，在构造方法中可以指定数据库名字和数据库版本
3. 实现 onCreate 方法，使用 TableUtils.createTable 方法创建表
4. 实现 onUpgrade 方法，升级数据库

#### 4. 在 ApplicationTest 中添加测试代码
```java
public class ApplicationTest extends ApplicationTestCase<Application> {
    public ApplicationTest() {
        super(Application.class);
    }

    public void testCreateDB(){
        DBHelper dbHelper=new DBHelper(getContext());
        dbHelper.getWritableDatabase();
    }
}
```


## 拓展和参考资料
- [ORMLite官网](http://ormlite.com/)

    http://ormlite.com/
    
- [Android 快速开发系列 ORMLite 框架最佳实践](http://blog.csdn.net/lmj623565791/article/details/39122981)

    http://blog.csdn.net/lmj623565791/article/details/39122981