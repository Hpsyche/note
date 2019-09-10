## Stream基本用法

```java
package hpsyche.stream;

import org.junit.Test;

import java.util.*;
import java.util.stream.Collectors;

/**
 * @author fuzihao
 * @date 2019/7/22 11:45
 */
public class StreamTest1 {
    class Fruit{
        private String name;
        private Integer price;

        public Fruit(String name, Integer price) {
            this.name = name;
            this.price = price;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public Integer getPrice() {
            return price;
        }

        public void setPrice(Integer price) {
            this.price = price;
        }

        @Override
        public String toString() {
            return "Fruit{" +
                    "name='" + name + '\'' +
                    ", price=" + price +
                    '}';
        }
    }
    @Test
    public void test1(){
        List<Fruit> list=new ArrayList();
        list.add(new Fruit("苹果",1));
        list.add(new Fruit("西瓜",1));
        list.add(new Fruit("香蕉",2));
        list.add(new Fruit("葡萄",3));
        //以水果名分类
//        Map<String, List<Fruit>> collect = list.stream().collect(Collectors.groupingBy(Fruit::getName));
//        Set<String> keys = collect.keySet();
//        for(String s:keys){
//            System.out.println(s+":"+collect.get(s));
//        }

        //水果价格大于1的所有水果
//        List<Fruit> fruits = list.stream().filter(x -> x.price > 1).collect(Collectors.toList());
//        for(Fruit fruit:fruits){
//            System.out.println(fruit);
//        }

        //以水果名分类
//        List<Fruit> fruits = list.stream().filter(x -> x.price == 1).collect(Collectors.toList());
//        for(Fruit fruit:fruits){
//            System.out.println(fruit);
//        }

        //判断是否为null，否则返回值
//        Fruit fruit=new Fruit(null,0);
//        String name=Optional.ofNullable(fruit.name).orElse("123");
//        System.out.println(name);

        //判断是否为null，否则抛出异常
//        Fruit fruit=new Fruit("dsa",0);
//        String name=Optional.ofNullable(fruit.name).orElseThrow(()->new RuntimeException(""));
//        System.out.println(name);

        //optional：优雅地处理null值
        Fruit fruit=new Fruit("dsa",0);
//        Fruit fruit=null;
        Optional<Integer> name=Optional.ofNullable(fruit).map(myfruit->myfruit.price);
        System.out.println(name.orElse(123));
    }
}

```

## Stream如何优化遍历？

官方将Stream中的操作分为两大类：中间操作（Intermediate operations）和终结操作（Terminal operations）。中间操作只对操作进行了记录，即只会返回一个流，不会进行计算操作，而终结操作是实现了计算操作。

中间操作又可以分为无状态（Stateless）与有状态（Stateful）操作，前者是指元素的处理不受之前元素的影响，后者是指该操作只有拿到所有元素之后才能继续下去。

终结操作又可以分为短路（Short-circuiting）与非短路（Unshort-circuiting）操作，前者是指遇到某些符合条件的元素就可以得到最终结果，后者是指必须处理完所有元素才能得到最终结果。操作分类详情如下图所示：

![](D:\Work\TyporaNotes\note\JavaSE\pict\1-1.jpg)

我们通常还会将中间操作称为懒操作，也**正是由这种懒操作结合终结操作、数据源构成的处理管道（Pipeline），实现了Stream的高效。**

## Stream源码实现

在了解Stream如何工作之前，我们先来了解下Stream包是由哪些主要结构类组合而成的，各个类的职责是什么。参照下图：

![](D:\Work\TyporaNotes\note\JavaSE\pict\1-2.jpg)

BaseStream和Stream为最顶端的接口类。BaseStream主要定义了流的基本接口方法，例如，spliterator、isParallel等；Stream则定义了一些流的常用操作方法，例如，map、filter等。

ReferencePipeline是一个结构类，他通过定义内部类组装了各种操作流。他定义了Head、StatelessOp、StatefulOp三个内部类，实现了BaseStream与Stream的接口方法。

Sink接口是定义每个Stream操作之间关系的协议，他包含begin()、end()、cancellationRequested()、accpt()四个方法。ReferencePipeline最终会将整个Stream流操作组装成一个调用链，而这条调用链上的各、个Stream操作的上下关系就是通过Sink接口协议来定义实现的。

## Stream操作叠加

我们知道，一个Stream的各个操作是由处理管道组装，并统一完成数据处理的。在JDK中每次的中断操作会以使用阶段（Stage）命名。

管道结构通常是由ReferencePipeline类实现的，前面讲解Stream包结构时，我提到过ReferencePipeline包含了Head、StatelessOp、StatefulOp三种内部类。

Head类主要用来定义数据源操作，在我们初次调用names.stream()方法时，会初次加载Head对象，此时为加载数据源操作；接着加载的是中间操作，分别为无状态中间操作StatelessOp对象和有状态操作StatefulOp对象，此时的Stage并没有执行，而是通过AbstractPipeline生成了一个中间操作Stage链表；当我们调用终结操作时，会生成一个最终的Stage，通过这个Stage触发之前的中间操作，从最后一个Stage开始，递归产生一个Sink链。如下图所示：

![](D:\Work\TyporaNotes\note\JavaSE\pict\1-3.jpg)

下面我们再通过一个例子来感受下Stream的操作分类是如何实现高效迭代大数据集合的。

```java
List<String> names = Arrays.asList("张三", "李四", "王⽼五", "李三", "刘⽼四", "王⼩⼆", "张四", "张五六七");
String maxLenStartWithZ = names.stream()
.filter(name -> name.startsWith("张"))
.mapToInt(String::length)
.max()
.toString();
```

这个例子的需求是查找出一个长度最长，并且以张为姓氏的名字。从代码角度来看，你可能会认为是这样的操作流程：首先遍历一次集合，得到以“张”开头的所有名字；然后遍历一次filter得到的集合，将名字转换成数字长度；最后再从长度集合中找到最长的那个名字并且返回。

这里我要很明确地告诉你，实际情况并非如此。我们来逐步分析下这个方法里所有的操作是如何执行的。

首先 ，因为names是ArrayList集合，所以**names.stream()方法将会调用集合类基础接口Collection的Stream：**

```java
default Stream<E> stream() {
	return StreamSupport.stream(spliterator(), false);
}
```

然后，Stream方法就会调用StreamSupport类的Stream方法，方法中**初始化了一个ReferencePipeline的Head内部类对象：**

```java
public static <T> Stream<T> stream(Spliterator<T> spliterator, boolean parallel) {
    Objects.requireNonNull(spliterator);
    return new ReferencePipeline.Head<>(spliterator,
        StreamOpFlag.fromCharacteristics(spliterator),
        parallel);
}
```

再调用filter和map方法，这两个方法都是无状态的中间操作，所以**执行filter和map操作时，并没有进行任何的操作，而是分别创建了一个Stage来标识用户的每一次操作。**

而通常情况下Stream的操作又需要一个回调函数，所以一个完整的Stage是由数据来源、操作、回调函数组成的三元组来表示。

new StatelessOp将会调用父类**AbstractPipeline的构造函数，这个构造函数将前后的Stage联系起来，生成一个Stage链表**。

因为在创建每一个Stage时，都会包含一个opWrapSink()方法，该方法会把一个操作的具体实现封装在Sink类中，Sink采用（处理->转发）的模式来叠加操作。

当执行max方法时，会调用ReferencePipeline的max方法，此时由于max方法是终结操作，所以会创建一个TerminalOp操作，同时创建一个**ReducingSink，并且将操作封装在Sink类中。**

最后，调用AbstractPipeline的wrapSink方法，**该方法会调用opWrapSink生成一个Sink链表，Sink链表中的每一个Sink都封装了一个操作的具体实现。**

当Sink链表生成完成后，Stream开始执行，通过spliterator迭代集合，执行Sink链表中的具体操作。

```java
@Override
final <P_IN> void copyInto(Sink<P_IN> wrappedSink, Spliterator<P_IN> spliterator) {
    Objects.requireNonNull(wrappedSink);
    if (!StreamOpFlag.SHORT_CIRCUIT.isKnown(getStreamAndOpFlags())) {
        wrappedSink.begin(spliterator.getExactSizeIfKnown());
        spliterator.forEachRemaining(wrappedSink);
        wrappedSink.end();
    }
    else {
    	copyIntoWithCancel(wrappedSink, spliterator);
	}
}
```

Java8中的Spliterator的forEachRemaining会迭代集合，每迭代一次，都会执行一次filter操作，如果filter操作通过，就会触发map操作，然后将结果放入到临时数组object中，再进行下一次的迭代。完成中间操作后，就会触发终结操作max。

## 合理使用Stream

**在循环迭代次数较少的情况下，常规的迭代方式性能反而更好；在单核CPU服务器配置环境中，也是常规迭代方式更有优势；而在大数据循环迭代中，如果服务器是多核CPU的情况下，Stream的并行迭代优势明显。所以我们在平时处理大数据的集合时，应该尽量考虑将应用部署在多核CPU环境下，并且使用Stream的并行迭代方式进行处理。**

用事实说话，我们看到其实使用Stream未必可以使系统性能更佳，还是要结合应用场景进行选择，也就是合理地使用Stream。

## 总结

在串行处理操作中，Stream在执行每一步中间操作时，并不会做实际的数据操作处理，而是将这些中间操作串联起来，最终由终结操作触发，生成一个数据处理链表，通过Java8中的Spliterator迭代器进行数据处理；此时，每执行一次迭代，就对所有的无状态的中间操作进行数据处理，而对有状态的中间操作，就需要迭代处理完所有的数据，再进行处理操作；最后就是进行终结操作的数据处理。

在并行处理操作中，Stream对中间操作基本跟串行处理方式是一样的，但在终结操作中，Stream将结合ForkJoin框架对集合进行切片处理，ForkJoin框架将每个切片的处理结果Join合并起来。最后就是要注意Stream的使用场景。

## 问答

```java
//将list1中的奇数转移到容器resList中
List<Integer> list1=new ArrayList<>();
for(int i=0;i<100;i++){
    list1.add(i);
}

List<Integer> resList=new ArrayList<>();
list1.stream().parallel().filter(i->i%2==1).forEach(resList::add);
for(Integer res:resList){
    System.out.println(res);
}

//output：
/**
65
67
63
71
73
69
...
*/
```

由于流是并行处理，parallelList会存在并发问题（可能会出现少数字、无序以及异常情况），应该使用collect方法聚合。

