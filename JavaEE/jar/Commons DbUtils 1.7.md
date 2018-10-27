# Commons DbUtils 1.7

简易操作数据库

和c3p0配合操作数据库

其中方法的实现是依靠反射机制实现的

## 增删改操作

1.创建QueryRunner，需要提供数据库连接池对象

QueryRunnerqr=newQueryRunner(JdbcUtils.getDataSource());

2.给出sql

String sql="insert into user values(?,?)";

3.给出参数数组

Object[] params={"大佬","666666"};

4.传入sql和参数数组，执行增删改操作

qr.update(sql,params);

## 查询操作

1.创建QueryRunner，需要提供数据库连接池对象

QueryRunnerqr=newQueryRunner(JdbcUtils.getDataSource());

2.给出sql

Stringsql="select*fromuserwhereusername=?";

3.执行query()方法，需要给出结果集处理器，即ResultSetHandler的实现类对象

我们要给出的是BeanHandler，它实现了ResultSetHandler

它需要一个类型，它会把rs中数据封装到指定类型的javabean对象中，然后返回到javabean中

注意：此对象需要数据库的属性的对象的属性及名称完全一致

Useruser=qr.query(sql,newBeanHandler<User>(User.class),"大佬");

System.out.println(user);

## QueryRunner

### update方法

int update(Stirng sql,Object ... params);可执行增删改操作 

Int update(Connection conn,String sql,Object …params):需要调用者提供Connection，这说明本方法不再管理Connection了(支持事务）

### query方法

T query(String sql,ResultSetHandler rsh,Object …params):可执行查询

它会先得到ResultSet，然后调用rsh的handle()把rs转换成需要的类型

T query(Connection conn,String sql,ResultSetHandler rsh,Object …params):（支持事务）

### ResultSetHandler接口

#### BeanHandler(单行)

构造器需要一个class类型的参数，用来把一行结果转换成指定类型的javaBean对象

#### BeanListHandler(多行)

构造器也是需要一个class类型的参数，用来把一行结果转换成指定类型的javaBean对象，

那么多行就是转换成List对象，一堆javaBean

#### MapHandler（单行）

把一行结果转换成Map对象

如：一行记录

Sid  sname  age  gender

1001   zs      99     male

一个map：

{sid:1001,sname:zs,age:99,gender:male}

#### MapListHandler(多行)

把一行记录转换成一个Map，多行就是多个Map，即List<Map>

#### ScalarHandler(单行单例)

通常用于select count(*) from stu语句！结果集是单行单列的，它返回一个Object

当得到count时，先转成number类型，再通过xxxValue()转换成想要的类型





