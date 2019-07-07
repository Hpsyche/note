## Vector

Vector是一个与ArrayList有着相同继承体系的类，大致功能也和ArrayList一样。Vector与ArrayList最大的不同点在于它是线程安全的，因为其内部几乎所有方法都用了synchronized来修饰。但是，Synchronized是重量级锁，读写操作也没有做适当的并发优化，已经被并发性更好的CopyOnWriteArrayList取代了。所以，当不要求线程安全时，自然会选择ArrayList，如果要求线程安全，往往也会选择CopyOnWriteArrayList或者Collections.synchronizedList()。

![](D:\Work\TyporaNotes\note\JavaSE\pict\vector之uml.png)

## Stack

Stack是Vector的子类，其内部的方法也都是通过无脑加synchronized来实现的，所以虽然线程安全，但是并发性不高。当不要求线程安全时，会选择LinkedList或者ArrayList（LinkedList的API更接近栈的操作，所以最佳选择是LinkedList），当要求线程安全时，我们会用java.util.concurrent包下的某些类。 

**虽然LinkedList的API比较接近栈的操作，但是暴露了许多用不着的方法，这会带来危险。**解决方法是编写一个LinkedList的包装类，只暴露与栈相关的方法。

```java
**
 * 包装{@code LinkedList}，使其仅暴露与栈相关的方法
 */
public class Stack<T> {
    private LinkedList<T> list;
    public Stack() {
        list = new LinkedList<>();
    }
    public void push(T item) {
        list.push(item);
    }
    public T pop() {
        return list.pop();
    }
    public T peek() {
        return list.peek();
    }
    public boolean isEmpty() {
        return list.isEmpty();
    }
    @Override
    public String toString() {
        return list.toString();
    }
}
```

## Hashtable

首先看看Hashtable的UML类图，关键点是其实现了Map接口，所以它是一个存储键值对的容器。通过查看源码，我们知道，其是一个线程安全的类，而且还是用synchronized来实现的，所以并发性不高。所以，当面对不要求线程安全的应用场景时我们会用HashMap代替，要求线程安全的应用场景我们往往也会用ConcurrentHashMap或者Collections.synchronizedMap()来代替。 

![](D:\Work\TyporaNotes\note\JavaSE\pict\hashtable之uml.png)

它与HashMap还有一个比较出名的不同点，就是它的散列表实现算法是用**线性探测法**实现的，该算法要求key不能为null，不然删除键值对时会出问题。另外还要求value不能为null。

```java
public synchronized V put(K key, V value) {
    // Make sure the value is not null
    if (value == null) { //value不能为null
        throw new NullPointerException();
    }
    // Makes sure the key is not already in the hashtable.
    Entry<?,?> tab[] = table;
    int hash = key.hashCode(); //key不可以为null
    int index = (hash & 0x7FFFFFFF) % tab.length;
    @SuppressWarnings("unchecked")
    Entry<K,V> entry = (Entry<K,V>)tab[index];
    for(; entry != null ; entry = entry.next) {
        if ((entry.hash == hash) && entry.key.equals(key)) {
            V old = entry.value;
            entry.value = value;
            return old;
        }
    }
    addEntry(hash, key, value, index);
    return null;
}
public synchronized boolean contains(Object value) {
    if (value == null) {
        throw new NullPointerException();//value不能为null
    }
    Entry<?,?> tab[] = table;
    for (int i = tab.length ; i-- > 0 ;) {
        for (Entry<?,?> e = tab[i] ; e != null ; e = e.next) {
            if (e.value.equals(value)) {
                return true;
            }
        }
    }
    return false;
}
```

## BigSet

如果要高效率的存贮大量开关信息，BitSet是很好的选择，不过它的效率是针对空间而言的；如果需要高效的访问时间，BitSet比本地数组稍慢一些。BitSet的最小容量是64位，如果存储的内容比较小，如8位。那么BitSet就浪费了一些空间。BitSet会随着元素的加入而扩充其容量。

如果你拥有一个可以命名的固定的标志集合，那么**EnumSet**与BitSet相比，更具有优势。因为EnumSet允许你按照名字而不是数字位置啦进行操作，可以减少错误。而且，EnumSet还可以防止你因不注意而添加新的位置标志。你应该使用BitSet的理由包括：只有在运行时才知道需要多少个标志位；对标志命名不合理；需要BitSet提供的特殊操作。

```java
public enum AlarmPoints {
	STAIR1, STAIR2, LOBBY, OFFICE1, OFFICE2, OFFICE3, OFFICE4, BATHROOM, UTILITY, KITCHEN
} // /:~
```

```java
import java.util.*;
import static enumerated.AlarmPoints.*;
import static net.mindview.util.Print.*;

public class EnumSets {
	public static void main(String[] args) {
		EnumSet<AlarmPoints> points = EnumSet.noneOf(AlarmPoints.class); // Empty
																			// set
		points.add(BATHROOM);
		print(points);
		points.addAll(EnumSet.of(STAIR1, STAIR2, KITCHEN));
		print(points);
		points = EnumSet.allOf(AlarmPoints.class);
		points.removeAll(EnumSet.of(STAIR1, STAIR2, KITCHEN));
		print(points);
		points.removeAll(EnumSet.range(OFFICE1, OFFICE4));
		print(points);
		points = EnumSet.complementOf(points);
		print(points);
	}
} 
/**
输出结果：
[BATHROOM]
[STAIR1, STAIR2, BATHROOM, KITCHEN]
[LOBBY, OFFICE1, OFFICE2, OFFICE3, OFFICE4, BATHROOM, UTILITY]
[LOBBY, BATHROOM, UTILITY]
[STAIR1, STAIR2, OFFICE1, OFFICE2, OFFICE3, OFFICE4, KITCHEN]
*/
```



