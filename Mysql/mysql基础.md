* 标准的SQL语句通常分为如下几种类型：

  * 查询语句：由select关键字完成；

  * DML（数据操作语言Data Manipulation Language）：由insert、update和delete三个关键字组成；

  * DDL（数据定义语言Data Definition Language）：由create、alert、drop和truncate四个关键字组成；

    *truncate？*

    * truncate:会清空表中所有的数据，速度快，不可回滚；实质是删除整张表包括数据再重新创建表；

      delete:逐行删除数据，每步删除都是有日志记录的，可以回滚数据；实质是逐行删除表中的数据；

    * truncate table在功能上与不带 where子句的 delete语句相同：二者均删除表中的全部行。但 truncate table比 delete速度快，且使用的系统和事务日志资源少。 delete语句每次删除一行，并在事务日志中为所删除的每行记录一项。

      truncate ,delete,drop放在一起比较：
      truncate table：删除内容、释放空间但不删除定义。
      delete table:删除内容不删除定义，不释放空间。
      drop table：删除内容和定义，释放空间。

  * DCL（数据控制语言Data Control Language）：由grant和revoke两个关键字组成；

  * 事务控制语句：主要由commit、rollback和savepoint三个关键字组成

* sql命名通常建议使用多个单词连缀而成，单词之间以_分割；

* 建立外键约束：

  #使用表级的约束语法建立外键约束，指定外键约束的约束名为student_teacher_fk

  constraint student_teacher_fk foreign key(java_teacher) references teacher_table2(teacher_id)

  删除外键约束：

  drop foreign key student_teacher_fk;

* 外键约束不仅可以参照其他表，也可以参照自身，例如，使用一个表保存某个公司的所有员工记录，员工之间有部门经理和普通员工之分，部分经理和普通员工之间存在一对多的关系，则有：

  ```
  create table foreign_test{
      ......
      ......
      foreign key(refer_id) references foreign_test(foreign_id)
  }
  ```

* 如果想定义删除主表记录时，从表记录而随之删除，则需要在建立外键约束后添加on delete cascade或添加on delete set null，第一种是删除主表记录时，关联的从表记录全部级联删除；第二种是把关联的从表记录主键设为null；

* 当执行SQL查询后可以通过移动记录指针来遍历resultSet的每天记录，但程序可能不清楚该resultSet里包含哪些数据列，以及每个数据列的数据类型，那么可以通过ResultSetMeteData来获取关于ResultSet的描述信息。(MetaData的意思是元数据，即描述其他数据的数据)

  ResultSet里包含一个getMetaData()方法，该方法返回该resultSet对应的ResultSetMetaData封装了ResultSet对象的数据。其中常用方法有：

  * int getColumnCount()：返回该ResultSet的列数量；
  * String getColumnName(int column)：返回指定索引的列名；
  * int getColumnType(int column)：返回指定索引的列类型

  虽然ResultSetMeteData可以准确地分析出ResultSet里包含多少列，以及类名、数据类型等信息，等使用其需要一定的系统开销，因此如果在编程过程中已经已经包含多少列等信息，则没必要使用该ResultSet对象。

* 在使用ResultSet时，程序得到ResultSet关闭后，再去通过ResultSet获取记录就会发生异常。这不利于JDBC编程的数据访问层和视图显示层的解耦，此时可以迭代访问ResultSet的记录，将其存了JavaBean中，但编程较为繁琐；另一种是需要Connection一直处于打开状态，但这样不仅不安全，而且对程序性能也有影响。

  因此，离线RowSet正是来处理此问题的，离散RowSet直接将底层数据读入内存中，封装成RowSet对象，并可以完全当做JavaBean来使用，不仅安全，编程也简单。

  RowSet的父接口CachedRowSet提供了分页功能，即一次只装载ResultSet的几条记录，避免CachedRowSet占用内存过大的问题，导致性能消耗。
  
  ## 对sql视图进行修改时是否会改变基本数据表数据
  
  会的。视图只是数据库在磁盘上的一个缩小范围的逻辑影像，任何修改都会修改到基本数据表的。  
  
  同理，如果改动了基本表，视图来源于这个基本表，那视图给你呈现的结果也会随之发生变化。
  
  为了不通过视图了修改数据，我们在创建视图时，可以通过with check option；来强制不允许修改视图的数据。

