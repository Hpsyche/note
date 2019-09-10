首先定义一个 输出类，其中有静态方法用于输出

```java
package com.hpsyche.stream;

/**
 * @author fuzihao
 * @date 2019/7/29 12:52
 */
public class AccessMethod {
    public static void printStr(String str){
        System.out.println("print value:"+str);
    }
}
```

此时有三种等价的方法来实现以上的输出方式

```java
package com.hpsyche.stream;

import java.util.Arrays;
import java.util.List;

/**
 * @author fuzihao
 * @date 2019/7/29 12:52
 */
public class StreamTest1 {
    public static void main(String[] args) {
        List<String> list= Arrays.asList("a","b","c");
        //普通的输出方式
        for(String s:list){
            AccessMethod.printStr(s);
        }
        //lambda输出
        list.forEach(s->{
            AccessMethod.printStr(s);
        });
        //双冒号方式（最简略），执行AccessMethod类中的printStr方法，其中参数的list各元素
        list.forEach(AccessMethod::printStr);
    }
}
```

同样的，map中两个参数（键与值）也可以以此方式实现：

首先自定义一个接收键值对参数的static方法

```java
package com.hpsyche.stream;

/**
 * @author fuzihao
 * @date 2019/7/29 12:57
 */
public class AccessMap {
    public static void set(String a,String b){
        System.out.println(a+":"+b);
    }
}=
```

在map中实现该方法

```java
package com.hpsyche.stream;

import java.util.HashMap;
import java.util.Map;

/**
 * @author fuzihao
 * @date 2019/7/29 12:56
 */
public class StreamTest2 {
    public static void main(String[] args) {
        Map<String,String> map=new HashMap<>();
        map.put("1","1");
        map.put("2","2");
        map.put("3","3");
        map.forEach(AccessMap::set);
    }
}
```



