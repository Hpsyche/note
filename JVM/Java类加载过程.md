# 类运行过程

java程序运行过程大致包括三部分：编译、加载、执行；

## 编译

即把我们写好的java文件，通过javac命令编译成字节码，也就是我们常说的.class文件。

## 加载

把class字节码文件从各个来源通过类加载器装载入内存中。

### 类加载器有四大类：

- 根加载器Bootstrap Class Loader
- 扩展加载器Extension Class Loader
- 系统应用加载器APP Class Loader
- 用户自定义加载器Customer Class Loader

| 名称                                         | 语言 | 默认加载路径                |
| -------------------------------------------- | ---- | --------------------------- |
| 根加载器                                     | C++  | JAVA_HOME/JRE/lib/rt.jar    |
| 扩展加载器<br />JDK9后改名为（平台类加载器） | JAVA | JAVA_HOME/JRE/lib/ext/*.jar |
| 系统应用加载器                               | JAVA | 系统classpath               |
| 用户自定义加载器                             | JAVA | 自定义目录                  |

Java程序在执行前先要检查类是否已经被加载。

### 检查过程

检查类是否已经被加载，从底层往上层依次检查各个加载器已经加载的类，顺序是系统应用类加载器、扩展加载器、根加载器，一旦发现被某个加载器加载过，则马上使用该类。**如果一直找到最顶层的根加载器，发现类还没有被加载进JVM运行数据区的方法区，则接下来就要加载该类**。

### 加载过程

加载过程和检查过程顺序相反，从上层往下层的顺序进行加载。从加载器检查自己的加载路径，找要加载的类，一旦找到类就进行加载。

从上可以得出：<font color=red>JVM不是一开始就把所有的类都加载进内存中，而是只有第一次遇到某个需要运行的类时才会加载，且只加载一次。</font>

## 执行

把编译生成的.class文件交给Java虚拟机(JVM)执行。

执行过程包括：链接和初始化。

### 链接过程

链接（linking）包括三个部分：

- 验证verifying：验证类符合Java规范和JVM规范，和编译阶段的语法语义分析不同。
- 准备preparing：<font color=red>为类的静态变量分配内存，并构造与该类相关联的方法表（注意：只是分配内存，具体的初始化工作会在稍后的初始化阶段进行）。</font>（<font color=blue>不初始化静态代码块</font>）。<font color=red>**对于final static修饰的变量，直接赋值为用户的定义值。**</font>
  - **static块的执行发生在“初始化”的阶段。**
- 解析resolving：将符号引用（字面量描述）转为直接引用（对象和实例的地址指针、实例变量和方法的偏移量）
  * 举个例子来说，现在调用方法hello()，这个方法的地址是1234567，那么hello就是符号引用，1234567就是直接引用。
    在解析阶段，虚拟机会把所有的类名，方法名，字段名这些符号引用替换为具体的内存地址或偏移量，也就是直接引用。

### 初始化过程

这个阶段主要是对类变量初始化，是执行类构造器< clinit >方法的过程；换句话说，<font color=red>只对static修饰的变量或语句进行初始化。（不对final static修饰的变量进行初始化）</font>

* 如果初始化一个类的时候，其父类尚未初始化，则优先初始化其父类。
* 如果同时包含多个静态变量和静态代码块，则按照自上而下的顺序依次执行。
* Java虚拟机会通过加锁来确保类的< clinit >方法仅被执行一次。

*类的初始化何时触发？*

* 当虚拟机启动时，初始化用户指定的主类；
* 当遇到用以新建目标类的实例的new指令时，初始化new指令的目标类；
* 当遇到调用静态方法的指令时，初始化该静态方法所在的类；
* 当遇到访问静态字段的指令时，初始化该静态字段所在的类；
* 子类的初始化会触发父类的初始化；
* 如果一个接口定义了 default 方法，那么直接实现或者间接实现该接口的类的初始化，会触发该接口的初始化；
* 使用反射 API 对某个类进行反射调用时，初始化这个类；
* 当初次调用 MethodHandle 实例时，初始化该 MethodHandle 指向的方法所在的类。

## 有问必答

* *什么是类加载器？*

  Java类加载器是Java运行时环境的一部分，负责动态加载Java类到Java虚拟机的内存空间中。类通常是按需加载，即第一次使用该类时才加载。由于有了类加载器，Java运行时系统不需要知道文件与文件系统。

  类加载器它是在虚拟机中完成的，负责动态加载Java类到Java虚拟机的内存空间中。类加载器负责读取 Java 字节代码，并转换成 `java.lang.Class`类的一个实例。

* *类加载器的双亲委派加载机制*

  当一个类收到了类加载请求，他首先不会尝试自己去加载这个类，而是把这个请求委派给父类去完成，每一个层次类加载都是如此，因此所有的加载请求都应该传送到启动类加载器中，只有当父类加载器反馈自己无法完成这个请求的时候（在它的加载路径里找不到这个所需要加载的类），子类加载器才会尝试自己去加载。

  过程如下：

  ![](D:\Work\TyporaNotes\note\JVM\pict\类加载过程.png)

* *tomcat中的类加载顺序：*

  1 使用bootstrap引导类加载器加载

    　2 使用system系统类加载器加载

    　3 使用应用类加载器在WEB-INF/classes中加载

    　4 使用应用类加载器在WEB-INF/lib中加载

    　5 使用common类加载器在CATALINA_HOME/lib中加载

  ![](D:\Work\TyporaNotes\note\JVM\pict\tomcat类加载过程.jpg)

* *Tomcat 不遵循双亲委派机制?Tomcat 如果使用默认的类加载机制行不行？*
  * 如果使用默认的类加载器机制，那么是无法加载两个相同类库的不同版本的，默认的累加器是不管你是什么版本的，只在乎你的全限定类名，并且只有一份。
  * web容器支持jsp的修改，即jsp 修改后不用重启；jsp 文件其实也就是class文件，那么如果修改了，但类名还是一样，类加载器会直接取方法区中已经存在的，修改后的jsp是不会重新加载的。其实，我们可以直接卸载掉这jsp文件的类加载器，所以你应该想到了，让每个jsp文件对应一个唯一的类加载器，当一个jsp文件修改了，就直接卸载这个jsp类加载器。重新创建类加载器，重新加载jsp文件。

* 代码实例

   ```java
   public class SSClass{
       static{
           System.out.println("SSClass");
       }
   }  
   
   public class SClass extends SSClass{
       static{
           System.out.println("SClass init!");
       }
   
       public static int value = 123;
   
       public SClass(){
           System.out.println("init SClass");
       }
   }
   
   public class SubClass extends SClass{
       static{
           System.out.println("SubClass init");
       }
   
       static int a;
   
       public SubClass(){
           System.out.println("init SubClass");
       }
   }
   
   public class NotInitialization{
       public static void main(String[] args){
           System.out.println(SubClass.value);
       }
   --------------------- 
   /* Output: 
           SSClass
           SClass init!
           123     
    */
   ```

   由以上可以得出：对于静态字段，只有直接定义这个字段的类才会被初始化，因此通过其子类来引用父类中定义的静态字段，只会触发父类的初始化而不会触发子类的初始化。在本例中，由于value字段是在类SClass中定义的，因此该类会被初始化；此外，<font color=red>在初始化类SClass时，虚拟机会发现其父类SSClass还未被初始化，因此虚拟机将先初始化父类SSClass，然后初始化子类SClass，**而SubClass始终不会被初始化。**</font>

   ```java
   public class ConstClass{
   
       static{
           System.out.println("ConstClass init!");
       }
   
       public static  final String CONSTANT = "hello world";
   }
   
   public class NotInitialization{
       public static void main(String[] args){
           System.out.println(ConstClass.CONSTANT);
       }
   }
   --------------------- 
   /* Output: 
           hello world
    */
   ```

   结果分析：只输出 “hello world”，这是因为虽然在Java源码中引用了ConstClass类中的常量CONSTANT，但是编译阶段将此常量的值“hello world”存储到了NotInitialization常量池中，对常量ConstClass.CONSTANT的引用实际都被转化为NotInitialization类对自身常量池的引用了。也就是说，实际上NotInitialization的Class文件之中并没有ConstClass类的符号引用入口，这两个类在编译为Class文件之后就不存在关系了。

