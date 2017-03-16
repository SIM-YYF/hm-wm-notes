# 017_整合OrmLite_关联关系查询
## 学习目标
- 熟练使用 OrmLite 进行增删改查

## 学习基础要求
熟悉 Android 中关系数据库 Sqlite 的使用

## 引言和回顾
上面我们讲了一下使用 ORMLite 创建数据库和数据表的一些基本的注解使用，当然还有其他注解和注解属性，不过大家不要着急，目前掌握这些就足够啦，其他的等用到的时候再讲解。下面我们来实践一下使用 ORMLite 进行数据的增删改查

## 课堂内容
### 1.插入 User 数据到 user 表
```java
public class ApplicationTest extends ApplicationTestCase<Application> {
    public ApplicationTest() {
        super(Application.class);
    }

    public void testUser() throws SQLException {
        DBHelper dbHelper=new DBHelper(getContext()); //1. 创建 Helper
        Dao<UserBean, Integer> dao = dbHelper.getDao(UserBean.class); //2. 创建 Dao
        UserBean userBean1=new UserBean();
        userBean1.set_id(1);
        dao.create(userBean1); //3.调用 Dao.create() 方法插入数据

        UserBean userBean2=new UserBean();
        userBean2.set_id(2);
        dao.create(userBean2);
    }
}
```
1. 创建 Helper 对象
2. 创建 Dao 对象，Dao<UserBean, Integer> UserBean 是表对应的 javabean，Integer 是表中主键的数据类型
3. 调用 Dao.create() 方法插入数据

### 2.插入 Address 数据到 address 表
```java
public class ApplicationTest extends ApplicationTestCase<Application> {
    public ApplicationTest() {
        super(Application.class);
    }
    ......
    public void testAddress() throws SQLException {
        DBHelper dbHelper=new DBHelper(getContext()); //1.创建 Helper
        Dao<AddressBean, Integer> dao = dbHelper.getDao(AddressBean.class); //2.创建 Dao 对象

        UserBean userBean=new UserBean();
        userBean.set_id(2);

        for(int i=1;i<10;i++) {
            AddressBean addressBean=new AddressBean();
            addressBean.set_id(i);
            addressBean.setGoodsAddress("送货地址"+i);
            addressBean.setVillage("xiaoqu"+i);
            addressBean.setUser(userBean);

            dao.create(addressBean);//3.调用 Dao.create() 方法插入数据
        }
    }
}
```

### 3.通过 id 查询 User 数据，实现多表链接查询
```java
public class ApplicationTest extends ApplicationTestCase<Application> {
    public ApplicationTest() {
        super(Application.class);
    }
    ......
    public void testFindById() throws SQLException {
        DBHelper dbHelper=new DBHelper(getContext());
        Dao<UserBean, Integer> dao = dbHelper.getDao(UserBean.class);

        UserBean userBean = dao.queryForId(1);//通过 dao.queryForId() 方法查询
        System.out.println(userBean.toString());
    }
}
```

## 问题
#### 1.使用 ORMLite 向数据库表中插入数据分为哪几步
4步；
1. 创建 Helper 对象；
2. 创建 Dao 对象；
3. 创建要插入的 javabean
4. 调用 Dao.create() 方法插入数据

