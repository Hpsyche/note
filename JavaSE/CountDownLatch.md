CountDownLatch被用来同步一个或多个任务，强制它们等待由其他任务执行的一组操作完成。

可以向CountDownLatch丢下设置一个初始计数值，任何在这个对象上调用wait()的方法都将阻塞，直至这个计数器值到达0。其他任务在结束其工作时，可以在该对象上调用countDown()来减小这个计数值。**CountDownLatch被设计为只能触发一次，计数值不能重置。若想要重置，需要使用CyclicBarrier。**

注意：**调用countDown()方法时这个调用被没有被阻塞，只有对await()的调用会被阻塞，直至计数值达到0。**

如下例

```java
package hpsyche.thread;

import java.util.Random;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

/**
 * @author fuzihao
 * @date 2019/7/6 10:18
 */
public class CountDownLatchDemo {
    static final int size=10;
    public static void main(String[] args) {
        ExecutorService executorService= Executors.newCachedThreadPool();
        CountDownLatch latch=new CountDownLatch(size);
        for (int i = 0; i < 10; i++) {
            executorService.execute(new WaitingTask(latch));
        }
        for (int i = 0; i < 10; i++) {
            executorService.execute(new TaskPortion(latch));
        }
        System.out.println("Launched all tasks");
        executorService.shutdown();
    }
}

class TaskPortion implements Runnable{
    private static int counter=0;
    private final int id=counter++;
    private static Random rand=new Random(47);
    private final CountDownLatch latch;

    public TaskPortion(CountDownLatch latch) {
        this.latch = latch;
    }

    @Override
    public void run() {
        try{
            doWork();
            latch.countDown();
            System.out.println(1);
        }catch (InterruptedException ex){
        }
    }

    private void doWork() throws InterruptedException {
        TimeUnit.MILLISECONDS.sleep(rand.nextInt(2000));
        System.out.println(this+" completed");
    }

    @Override
    public String toString(){
        return String.format("%1$-3d",id);
    }
}

class WaitingTask implements Runnable{
    private static int counter=0;
    private final int id=counter++;
    private final CountDownLatch latch;

    WaitingTask(CountDownLatch latch) {
        this.latch = latch;
    }

    @Override
    public void run() {
        try{
            latch.await();
            System.out.println("Latch barrier passed for"+this);
        }catch (InterruptedException e){
            System.out.println(this+" interrupted");
        }
    }

    @Override
    public String toString() {
        return String.format("WaitingTask %1$-3d",id);
    }
}
```

输出结果：

```tex
Launched all tasks
7   completed
1
9   completed
1
5   completed
1
8   completed
1
1   completed
1
2   completed
1
6   completed
1
4   completed
1
0   completed
1
3   completed
1
Latch barrier passed forWaitingTask 0  
Latch barrier passed forWaitingTask 2  
Latch barrier passed forWaitingTask 3  
Latch barrier passed forWaitingTask 5  
Latch barrier passed forWaitingTask 7  
Latch barrier passed forWaitingTask 4  
Latch barrier passed forWaitingTask 6  
Latch barrier passed forWaitingTask 9  
Latch barrier passed forWaitingTask 8  
Latch barrier passed forWaitingTask 1  
```

由以上输出可见， latch.await();的线程将会被阻塞，直到countDown的count计数器达到0。

 