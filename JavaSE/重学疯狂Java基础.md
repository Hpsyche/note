[TOC]

## 基础语法部分

* true false null不是关键字，是直接量

* 精确保存浮点数，考虑使用bigDecimal：

  * 因为java使用二进制数据的科学计数法来表示浮点数，因此可能不能精确表示一个浮点数。例如，我们把5.234564654f值赋给一个float类型变量，接着输出这个变量时看到这个变量的值已经发生了改变。使用double类型的浮点数比float类型的浮点数更精确，但如果浮点数的精度足够高（小数点的数字很多时），依然可能发生这种情况。
    如果需要精确保存一个浮点数，可以考虑使用BigDecimal类。

* java7新增的数值中允许使用下划线，让数值更容易读取

  ```java
  public static void main(String[] args) {
  		int a=123_456_789;
  		System.out.println(a);
      
  //print:123456789    
  ```

* 所有的byte、short、char类型将被提升到int类型

  short i=5;
  i=i-1；

  报错！i-1提升到int类型，再赋值给short将报错；

* ```java
  String s0="hello";
  Stirng s1="hello";
  String s2="he"+"llo";
  sout(s0==s1);
  sout(s0==s2);
  
  //print:
  // true true
  ```

  ​	Java会确保每个字符串常量只有一个，不会产生多个副本，例子中的s0和s1的“hello”都是字符串常量，它们在编译期就被确定了；而he和llo都是字符串常量，当一个字符串由多个字符串常量连接而成时，它本身也是字符串常量，**s2同样在编译期被解析成一个字符串常量，所以s2也是常量池中“hello”的引用。**

* 对于int类型的整数移位a>>b，当b>>32时， 系统先用b对32求余，因为（int类型只有32位），得到的结果才是真正移位的位数；即a>>33与a>>1得到结果相等。

* 对于long类型的整数移位a>>b，当b>>64时， 系统先用b对64求余，因为（long类型是64位），得到的结果才是真正移位的位数；即a>>65与a>>1得到结果相等。

*  switch只能是byte、short、char、int和枚举类型（在jdk1.7及以后版本支持 string）

* java交换元素：

  ```java
   class DataSwap{
       public int a;
       public int b;
   }
   public Class SwitchData(){
       public static void swap(DataSwap ds){
           int temp=ds.a;
           ds.a=ds.b;
           ds.b=temp;
       }
   }
   public static void main(String[] args){
       DataSwap ds=new DataSwap();
       ds.a=6;
       ds.b=9;
       swap(dw);
       sout(a+","+b);
   }
  ```

* 对于包装：

  有以下：System.out.println(new Integer(1)==new Integer(1));  

  Output:flase

  在Jdk1.5之后支持所谓的自动装箱，但可能会出现一些特殊情况，如下

  ```java
      public static void main(String[] args) {
          Integer i=2;
          Integer i2=2;
          System.out.println(i==i2);
          Integer i3=128;
          Integer i4=128;
          System.out.println(i3==i4);
      }
      //Output:true flase
  ```

  在进行自动拆装箱时，编译器会使用Integer.valueof()来创建Integer实例。

  以下是Integer.valueof()的源代码：

    public static Integer valueOf(int i) {
          assert IntegerCache.high >= 127;
          if (i >= IntegerCache.low && i <= IntegerCache.high)
              return IntegerCache.cache[i + (-IntegerCache.low)];
          return new Integer(i);
      }

  简单地解释这段代码，就是如果传入的int在IntegerCache.low和IntegerCache.high之间，那就尝试看前面的缓存中有没有打过包的相同的值，如果有就直接返回，否则就创建一个Integer实例。IntegerCache.low 默认是-128；IntegerCache.high默认是127.

  其实是，系统把一个-128--127之间的整数自动装箱成Integer实例，并放入一个名为cache的数组中缓存起来，如果以后把一个-128--127之间的整数自动装箱成一个Integer实例时，实际上是直接指向相应的数组元素，因此-128--127之间的同一个整数自动装箱成Integer实例时，永远都是引用cache数组的同一个元素，所以它们全部相等，在此范围外则不然；

* final修饰基本类型变量和引用类型变量的区别

  当使用final修饰基本变量时，不能对基本变量重新赋值，因此基本类型变量不能被改变；

  但对于引用类型变量而言，它保存的仅仅是一个引用，final只保证这个引用类型变量所引用的地址不会改变，即一直引用同一个对象，但这个对象完全可以改变。

* 枚举类型

  ```java
  public enum Operation {
      PLUS,MINUS,TIMES,DIVIDE;
      double eval(double x,double y){
          switch (this){
              case PLUS:return x+y;
              case MINUS:return x-y;
              case TIMES:return x*y;
              case DIVIDE:return x/y;
              default:return 0;
          }
      }
  
      public static void main(String[] args) {
          System.out.println(Operation.PLUS.eval(3,4));
      }
  }
  ```

  其中，default：return 0,完全没有存在的必要，因为this代表Operation枚举类的一个值，这个值只可能是PLUS等其中一个值，根本没有其他的值，但我们又不能不写，否则无法通过编译。

## 对象部分

* 当一个对象在堆内存中运行时，可以把它所处的状态分为以下三种：

  * 可达状态：当一个对象被创建后，若有一个以上的引用变量去引用它，则这个对象在程序中处于可达状态，程序可通过引用变量来调用该对象的Field和方法；

  * 可恢复状态：如果程序中某个对象不再有任何引用变量引用它，它就进入了可恢复状态，在这种状态下，系统的垃圾回收机制准备回收该对象所占用的内存，在回收该对象之前，系统会调用所有可恢复状态对象的finalize()方法进行资源清理。

    <font color=red>如果系统在调用finalize()方法时重新让一个引用变量引用该对象，则这个对象会再次变为可达状态；否则该对象将进入不可达状态。</font>

  * 不可达状态：当对象与所有引用变量的关联都被切断，且系统已经调用所有对象的finalize()方法后依然没有使该对象变成可达状态，那么这个对象将处于不可达状态时，系统才会真正回收该对象所占有的资源。

  克隆

  ```java
  public class CloneTest {
      static class User implements Cloneable{
          private Integer age;
          public User(Integer age) {
              this.age = age;
          }
          @Override
          protected User clone() throws CloneNotSupportedException {
              return (User) super.clone();
          }
      }
      public static void main(String[] args) throws CloneNotSupportedException {
          User u1=new User(10);
          User u2=u1.clone();
          System.out.println(u2.age);
          System.out.println(u1==u2);
      }
  }
  //output:10 false
  ```

  上面程序让User实现了Cloneable接口，并且实现了clone方法（非必需），由输出结果可得，Clone机制只是对对象实例变量进行“简单复制”，如果实例变量的类型是引用类型，Object的Clone机制也只是简单地复制这个引用变量。

  ![](D:\Work\TyporaNotes\note\JavaSE\pict\克隆机制.PNG)

  * Random

    如果两个Random对象的种子相同，而且方法的调用顺序也相同，则它们会产生相同的数字序列。也就是说，Random产生的数字并不是真正随机的，而是一种伪随机。

    为了避免两个Random对象产生相同的数字序列，通常推荐使用当前时间作为Random对象的种子，即：

    ```java
    Random rand=new Random(System.currentTimeMills());
    ```

    ```java
  package hpsyche.string;
    
    import java.util.Random;
    
    /**
     * @author Hpsyche
   */
    public class MyTest {
      public static void main(String[] args) {
            Random random=new Random(1);
          Random random2=new Random(1);
            System.out.println(random.nextInt(10));
          System.out.println(random2.nextInt(10));
        }
    }
    
    //output:5 5
    //即输出都是一致的
    ```
  
  在多线程环境下使用ThreadLocalRandom的方式与使用Random基本类似：
  
  ```java
    ThreadLocalRandom rand=ThreadLocalRandom.current();
    //生成一个4-20之间的随机数
    int val=rand.nextInt(4,20);
  ```
  
* Pattern、Matcher
  
  Pattern对象是正则表达式编译后内存中的表达形式，因此，正则表达式字符串必须先被编译为Pattern对象，然后再利用该Pattern对象创建对应的Matcher对象，执行匹配所涉及的状态保留在Matcher对象中，多个Matcher对象共享同一个Pattern对象。
  
  典型的调用顺序如下：
  
  ```java
    //将一个字符串编译成Pattern对象
  Pattern p=Pattern.compile("a*b");
    //使用Pattern对象创建Matcher对象
    Matcher m=p.matcher("aaaaab);
    boolean b=m.matches();
  ```
  
    上面定义的Pattern对象可以多次重复使用，如果某个正则表达式仅需使用一次，则可以直接使用Pattern类的静态matches方法，即
  
    ```java
    boolean b=Pattern.matches("a*b","aaaaab");
    ```
  
    不过此方式每次都需要重新编译新的Pattern对象，不能重复利用以编译的Pattern对象。
  
  * 变量的自增操作 i++，分三个步骤：
  
    ①从内存中读取出变量 i 的值
  
    ②将 i 的值加1
  
    ③将 加1 后的值写回内存
  
    这说明 i++ 并不是一个原子操作。因为，它分成了三步，有可能当某个线程执行到了第②时被中断了，那么就意味着只执行了其中的两个步骤，没有全部执行。

## 集合部分

### 固定长度的List

Arrays.ArrayList是一个固定长度的List集合，程序只能遍历访问集合里的元素，不可以增加、删除该集合里的元素。

### Queue集合

Queue用于模拟队列，即“先进先出”，有如下方法：

void add(Object e);
Object element(); （获取队列头部的元素，但是不是删除元素）
boolean offer(Object e):将制定元素加入此队列的尾部，当使用有容量限制的队列时，此方法通常会比add(Object e)更好
Object peek()：获取队列头部的元素，但是不删除元素，如果队列为空，则返回null
Object poll()：获取队列头部的元素，并删除该元素，如果队列为空，则返回null
Object remove()：获取队列头部的元素，并删除该元素

### Deque接口

Queue还有一个接口Deque，Deque代表“双向队列”，即其既可以当成队列使用，也可以当成栈使用。

其中如下方法：
addFirst\addLast\getFirst\getLast等方法。

### Hashtable

从Hashtable的类名就可以看出其实一个古老的类，它的命名甚至没有遵守Java的命名规范：驼峰命名规范，我们尽量少用Hashtable，即使需要创建线程安全的Map实现类，也无须使用Hashtable实现类，可以通过后面介绍的Collections工具把HashMap变成线程安全的。

### Set与Map

注意：Set和Map的关系十分紧密，Java源码就是先实现了HashMap、TreeMap等集合，然后通过包装一个所有的Value均为null的Map集合来实现Set集合。

### EnumMap

EnumMap中的所有的key都必须是某个枚举类的枚举值，其根据key的自然顺序来维护key-value对的顺序；

下面程序示例了EnumMap的用法。

```java
enum Season{
    SPRING,SUMMER,FALL,WINTER
}
public class EnumMapTest{
    public static void main(String[] args){
        //创建一个EnumMap对象，该EnumMap的所有key必须是Season的枚举值
        EnumMap enumMap=new EnumMap(Season.class);
        enumMap.put(Season.SUMMER,"夏日炎炎");
        enumMap.put(Season.SPRING,"春暖花开");
        System.out.println(enumMap);
    }
}
```

运行结果如下：

{SPRING=春暖花开,SUMMER=夏日炎炎}

### 各Map实现类的性能分析

HashMap通常比Hashtable要快一点，因为Hashtable需要额外的线程同步控制；

TreeMap比上两个都要慢，因为TreeMap底层采用红黑树来管理key-value对，但使用TreeMap有一个好处，其键值对总是处于有序状态，当TreeMap被填充之后，就可以调用keySet()，取得由key组成的set，然后使用toArray()方法生成key的数组，接下来使用Arrays的binarySearch()方法在已排序的素组中快速地查询对象。

在一般场景，考虑使用HashMap，在需要排序时，使用TreeMap；

EnumMap的性能最好，但它只能使用同一个枚举类的枚举值作为key。

## 异常部分

### 不要忽略捕获到的异常

catch块为空时，所有的人都看不到异常，但整个应用可能已经彻底换了，仅在catch块里打印错误跟踪栈信息稍微好一点，但仅比空白多了几行异常信息，通常建议对异常采取适当措施，比如：

* 处理异常。对异常进行合适的修复，然后绕过异常发生的地方继续执行；或者用别的数据进行计算，以代替期望的方法返回值；或者提示用户重新操作------总之，对于Checked异常，程序应该尽量修复。
* 重新抛出新异常：把当前运行环境下能做的事情尽量做完，然后进行异常转译，把异常包装成当前层的异常，重新抛出给上层调用者；
* 在合适的层处理异常。如果当前层不清楚如何处理异常，就不要在当前层使用catch语句来捕获该异常，直接使用throws抛出该异常，让上层调用者来负责处理该异常。

## 线程部分

###  包装线程不安全的集合

可以使用Collections提供的体态方法把这些集合包装成线程安全的集合，如下：

```java
<T> Collection<T> synchronizedCollection(Collection<T> c):返回指定collection对应的线程安全的collection；
static <T>List<T> synchronizedList(List<T> list):返回指定List对象对应的线程安全的List对象；
static <K,V>Map<K,V> synchronizedMap(Map<K,V> m):返回指定Map对象对应的线程安全的Map对象；
static <T>Set<T> synchronizedSet(Set<T> s):返回指定Set对象对应的线程安全的Set对象；
同理synchronizedSortedMap\synchronizedSortedSet
```

例如，我们要在多线程中使用线程安全的HashMap对象，可以采用如下代码：

```java
HashMap m=Collections.synchronizedMap(new HashMap());
```

如果需要把某个集合包装成线程安全的集合，应该在创建之后立即包装。

### 线程安全的集合类

从Java5开始，**在java.util.concurrent包下提供了大量支持高效并发访问的集合接口和实现类；**

大致可以分为两大类：

* 以Concurrent开头的集合类，如ConcurrentHashMap\ConcurrentLinkedQueue等；
* 以CopyOnWrite开头的集合类，如CopyOnWriteArrayList

其中以Concurrent开头的集合类代表了支持并发访问的集合，它们可以支持多个线程并发写入访问，这些写入线程的所有操作都是线程安全的，但读取操作不必锁定，以Concurrent开头的集合类采用了更复杂的算法来保证永远不会锁住整个集合，因此在并发写入时有较好的性能。

对于CopyOnWrite开头的集合类，正如其名字所暗示的，它采用复制底层数组的方式来实现写操作。
当线程对集合执行读取操作时，线程将会直接读取集合本身，无需加锁与阻塞；当线程对集合执行写操作时，该集合会在底层复制一份新的数组，对新的数组执行写入操作，因此同时性能也比较好。

由此可见，对于CopyOnWrite开头的集合类适用于读取操作远远大于写入操作的场景中，例如缓存等。

## 序列化

如果需要让某个对象支持序列化机制，为了让某个类是可序列化的，该类必须实现如下两个接口之一：

* Serializable
* Externalizable

**所有可能在网络上传输的对象的类必须是可序列化的，否则程序将会出现异常；所有需要保存到磁盘的对象的类都必须可序列化，比如Web应用中需要保存到HttpSession或ServletContext属性的Java对象。**

### 对象引用的序列化

若某个类的Field是另一个引用类型的，那么这个引用类必须是可序列化的，否则拥有该类型的Field的类也是不可序列化的。

