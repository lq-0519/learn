# 守护线程

守护线程的优先级比较低，用于为系统中的其它对象和线程提供服务。

守护线程应该永远不去访问固有资源，如：数据库、文件等。因为它会在任何时候甚至在一个操作的中间发生中断。

## isDaemon()

`Thread`类的`isDaemon()`方法检查线程是否是守护程序线程。 如果线程是守护进程线程，则此方法将返回`true`，否则返回`false`。

原文链接：https://www.yiibai.com/java_multithreading/java-thread-isdaemon-method.html 

# CountDownLatch、CyclicBarrier和Semaphore

原文链接: [Java并发编程：CountDownLatch、CyclicBarrier和Semaphore - Matrix海子 - 博客园 (cnblogs.com)](https://www.cnblogs.com/dolphin0520/p/3920397.html)

## CountDownLatch

适用场景: 老师将一百张卷子发给一百个同学去做, 老师需要等到最后一个同学交卷了才能走, 类比到代码中就是, 老师启动一百个学生做卷子的线程, 老师需要等待这一百个线程全部执行完毕才能进行下一步(离开教室)

然后下面这3个方法是CountDownLatch类中最重要的方法：

```java
public void await() throws InterruptedException { }; // 等待count的值为0继续下一步
public boolean await(long timeout, TimeUnit unit) throws InterruptedException { }; // 等待timeout时间之后进行下一步, 否则超时
public void countDown() { };  //将count值减1
```

示例:

```java
public class Test {
     public static void main(String[] args) {   
         final CountDownLatch latch = new CountDownLatch(2);
          
         new Thread(){
             public void run() {
                 try {
                     System.out.println("子线程"+Thread.currentThread().getName()+"正在执行");
                    Thread.sleep(3000);
                    System.out.println("子线程"+Thread.currentThread().getName()+"执行完毕");
                    latch.countDown();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
             };
         }.start();
          
         new Thread(){
             public void run() {
                 try {
                     System.out.println("子线程"+Thread.currentThread().getName()+"正在执行");
                     Thread.sleep(3000);
                     System.out.println("子线程"+Thread.currentThread().getName()+"执行完毕");
                     latch.countDown();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
             };
         }.start();
          
         try {
             System.out.println("等待2个子线程执行完毕...");
            latch.await();
            System.out.println("2个子线程已经执行完毕");
            System.out.println("继续执行主线程");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
     }
}
```

## CyclicBarrier

适用场景: 假如有10个线程并行进行写操作, 要求10个线程全部执行完写操作才能进行接下来的读操作, 此时就可以使用CyclicBarrier了

代码示例

```java
public class Test {
    public static void main(String[] args) {
        int N = 4;
        CyclicBarrier barrier  = new CyclicBarrier(N);
        for(int i=0;i<N;i++)
            new Writer(barrier).start();
    }
    static class Writer extends Thread{
        private CyclicBarrier cyclicBarrier;
        public Writer(CyclicBarrier cyclicBarrier) {
            this.cyclicBarrier = cyclicBarrier;
        }
 
        @Override
        public void run() {
            System.out.println("线程"+Thread.currentThread().getName()+"正在写入数据...");
            try {
                Thread.sleep(5000);      //以睡眠来模拟写入数据操作
                System.out.println("线程"+Thread.currentThread().getName()+"写入数据完毕，等待其他线程写入完毕");
                cyclicBarrier.await();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }catch(BrokenBarrierException e){
                e.printStackTrace();
            }
            System.out.println("所有线程写入完毕，继续处理其他任务...");
        }
    }
}
```

CyclicBarrier提供两个构造方法

```java
public CyclicBarrier(int parties, Runnable barrierAction) {
    // barrierAction 当parties个线程到达barrier状态后执行指定动作:barrierAction
    // barrierAction -> 不是所有的线程都会执行barrierAction操作, 而是选择其中一个线程去执行barrierAction操作
}
 
public CyclicBarrier(int parties) {
}
```

await方法

```java
public int await() throws InterruptedException, BrokenBarrierException { };
public int await(long timeout, TimeUnit unit)throws InterruptedException,BrokenBarrierException,TimeoutException { };
```

## Semaphore

类似于大学里学习操作系统时学习的资源锁, 我们可以设置资源的数量, 通过acquire()方法获取资源, 获取到资源的线程才能执行下一步操作, 操作执行完之后通过方法release()方法释放资源

更多使用细节查看原文链接

## 总结

CountDownLatch和CyclicBarrier都能够实现线程之间的等待，只不过它们侧重点不同：CountDownLatch一般用于某个线程A等待若干个其他线程执行完任务之后，它才执行；而CyclicBarrier一般用于一组线程互相等待至某个状态，然后这一组线程再同时执行；另外，CountDownLatch是不能够重用的，而CyclicBarrier是可以重用的。

Semaphore其实和锁有点类似，它一般用于控制对某组资源的访问权限。

# ThreadLocal

[Java面试必问：ThreadLocal终极篇 淦！ - 敖丙 - 博客园 (cnblogs.com)](https://www.cnblogs.com/aobing/p/13382184.html)

[(14条消息) JAVA中ThreadLocal用法介绍_蒋固金（jianggujin）的专栏-CSDN博客_java threadlocal](https://blog.csdn.net/jianggujin/article/details/51064034)

可以做到数据隔离, 让每个线程从ThreadLocal中都可以获取到自己专属的变量副本, 自己线程对自己的变量副本无论如何操作都不会影响到其他线程的变量副本

## 代码示例

```java
public class ThreadLocalDemo implements Runnable{
   private final static ThreadLocal<People> threadLocal = new ThreadLocal<People>();

   public static void main(String[] agrs){
      ThreadLocalDemo td = new ThreadLocalDemo();
       // 创建两个线程
      Thread t1 = new Thread(td);
      Thread t2 = new Thread(td);
      t1.start();
      t2.start();
   }

   public void run(){
      // 产生一个随机数设置人的age
      Random random = new Random();
      int age = random.nextInt(100);
      People people = threadLocal.get();
      // 线程首次执行此方法的时候，threadLocal.get()肯定为null
      if (people == null){
         // 创建一个对象，并保存到本地线程变量threadLocal中
         people = new People();
         threadLocal.set(people);
      }
      people.setAge(age);
      System.out.println("age is:"+ people.getAge());
   }
}

class People{
   private int age = 0;

   public int getAge(){
      return this.age;
   }

   public void setAge(int age){
      this.age = age;
   }
}
```

### 需要注意的点

1. 在一个线程中多次调用get方法获取到的都是同一个变量
2. 只有当线程消失之后, 线程的副本变量才会被gc回收, 所以在线程池中, 核心线程的变量副本不会垃圾回收器回收

## 使用场景

上线后发现部分用户的日期居然不对了，排查下来是`SimpleDataFormat`的锅，当时我们使用SimpleDataFormat的parse()方法，内部有一个Calendar对象，调用SimpleDataFormat的parse()方法会先调用Calendar.clear（），然后调用Calendar.add()，如果一个线程先调用了add()然后另一个线程又调用了clear()，这时候parse()方法解析的时间就不对了。

其实要解决这个问题很简单，让每个线程都new 一个自己的 `SimpleDataFormat`就好了，但是1000个线程难道new1000个`SimpleDataFormat`？

所以当时我们使用了线程池加上ThreadLocal包装`SimpleDataFormat`，再调用initialValue让每个线程有一个`SimpleDataFormat`的副本，从而解决了线程安全的问题，也提高了性能。

# ForkJoin

[使用ForkJoin - 廖雪峰的官方网站 (liaoxuefeng.com)](https://www.liaoxuefeng.com/wiki/1252599548343744/1306581226487842)

关键字: extends RecursiveTask<T> 创建子任务, invokeAll(subtask1, subtask2); 执行两个子任务, ForkJoinPool.commonPool().invoke(task)  启动任务;







