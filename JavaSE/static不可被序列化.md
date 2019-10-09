实体类

```java
public class User implements Serializable{
    private static final long serialVersionUID = 1L;
    private String name;
    private transient String password;   //被transient修饰的变量
    private static String age;
}
```

测试类

```java
public class Test {

    public static void main(String args[]) throws FileNotFoundException, IOException, ClassNotFoundException {
        User user = new User();
        user.setAge("22");
        user.setName("小明");
        user.setPassword("admin");
        System.out.println(user.getAge()+"\t"+user.getName()+"\t"+user.getPassword());
        ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("e:/user.txt"));
        oos.writeObject(user);
        oos.flush();
        oos.close();

        ObjectInputStream ois = new ObjectInputStream(new FileInputStream("e:/user.txt"));
        User users = (User) ois.readObject();

        System.out.println(users.getAge()+"\t"+users.getName()+"\t"+users.getPassword());
    }
}
```

从运行结果可以看出用  transient 修饰的变量在反序列化后值为 null

那这里被static修饰的变量反序列化后有值？

原因：静态变量在方法区,本来流里面就没有写入静态变量,我们打印静态变量当然会去方法区查找,我们当前 jvm 中有所以静态变量在序列化后任然有值。

被static修饰的变量应该也是不会被序列化的，因为**只有堆内存会被序列化.所以静态变量会天生不会被序列化。**

验证代码：

```java
package hpsyche.seri;

import java.io.*;

/**
 * @author Hpsyche
 */
public class SerializableTest1 {
    public static void main(String[] args) throws IOException, ClassNotFoundException {
        User user=new User();
        user.setAge("22");
        user.setName("111");
        user.setPassword("232");
        System.out.println(user.getAge()+"\t"+user.getName()+"\t"+user.getPassword());
        ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("d:/1.txt"));
        oos.writeObject(user);
        oos.flush();
        oos.close();
        user.setAge("34");
        ObjectInputStream ois = new ObjectInputStream(new FileInputStream("d:/1.txt"));
        User users = (User) ois.readObject();
        System.out.println(user.getAge()+"\t"+user.getName()+"\t"+user.getPassword());
    }
}
```

输出：

```tex
22	111	232
34	111	null
```

可以看到在序列化前 static 修饰的变量赋值为22，而反序列化后读取的这个变量值为34，由此可以看出 static 修饰的变量本身是不会被序列化的。

**我们读取的值是当前jvm中的内存（方法区）中对应此变量的值，所以最后输出的值为我们对static 变量后赋的值**

