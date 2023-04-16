## **理解线程的基本概念**

**首先：**理解三个概念 > 程序、进程、线程

- **程序（program）：**是为完成特定任务、用某种语言编写的一组指令的集合。即指<u>一段静态的代码</u>，静态对象。

- **进程（process）：**是程序的一次执行过程，<u>或是正在运行的一个程序。</u>是一个动态的过程：有它自身的产生、存在和消亡的过程。——生命周期

  - 如：运行中的QQ，运行中的MP3播放器 
  - 程序是静态的，进程是动态的 
  - <u>进程作为资源分配的单位</u>，系统在运行时会为每个进程分配不同的内存区域 

- **线程（thread）：**进程可以进一步细化为线程，是一个程序内部的一条执行路径。

  - 若一个进程同一时间**并行**执行多个线程，就是支持多线程的 

  - <u>**线程作为调度和执行的单位，每个线程拥有独立的运行栈和程序计数器(pc)**</u>，线程切换的开销小

  -  一个进程中的多个线程共享相同的内存单元/内存地址空间—>它们从同一堆中分配对象，可以 

    访问相同的变量和对象。这就使得线程间通信更简便、高效。但多个线程操作共享的系统资 

    源可能就会带来<u>安全的隐患</u>。



**单核CPU和多核CPU的理解**

- 单核CPU，其实是一种假的多线程，因为在一个时间单元内，也只能执行一个线程的任务。通过挂起的方式多个线程任务轮换执行。

- 如果是多核的话，才能更好的发挥多线程的效率。（现在的服务器都是多核的）

- 一个Java应用程序java.exe，其实至少有三个线程：main()主线程，gc() 

  垃圾回收线程，异常处理线程。当然如果发生异常，会影响主线程。 



**并行与并发**

- **并行：**多个CPU同时执行多个任务。比如：多个人同时做不同的事。
- **并发：**一个CPU（采用时间片）同时执行多个任务。多个人做同一件事。



*<u>以单核CPU为例，只使用单个线程先后完成多个任务（调用多个方</u>* 

*<u>法），肯定比用多个线程来完成用的时间更短，为何仍需多线程呢？</u>* 

**使用多线程的优点：**

1. 提高应用程序的响应。对图形化界面更有意义，可增强用户体验。
2. 提高计算机系统CPU的利用率
3. 改善程序结构。将既长又复杂的进程分为多个线程，独立运行，利于理解和修改

**需要用到多线程的场景**

- 程序需要同时执行两个或多个任务。 
- 程序需要实现一些需要等待的任务时，如用户输入、文件读写操作、网络操作、搜索等。 
- 需要一些后台运行的程序时。





**多线程重点：**

1. 线程的创建和使用：有四种线程创建的方法
2. 线程的同步：解决线程安全的问题

### 

### 如何在Java创建多线程

**创建多线程的四种方法：**

1. 创建多线程方法一：继承于Thread类
2. 创建多线程方法二：实现于Runnable接口
3. 创建多线程方法三：实现于Callable接口
4. 创建多线程方法四：使用线程池



**创建多线程方法一：继承于Thread类**

1. 创建一个继承于Thread类的子类
2. 重写Thread类的run() --> 将此线程执行的操作声明在run()中
3. 创建Thread类的子类的对象
4. 通过此对象调用start()
```java
//1. 创建一个继承于Thread类的子类
class MyThread extends Thread {
    //2. 重写Thread类的run()
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            if(i % 2 == 0){
                System.out.println(Thread.currentThread().getName() + ":" + i);
            }
        }
    }
}

public class ThreadTest {
    public static void main(String[] args) {
        //3. 创建Thread类的子类的对象
        MyThread t1 = new MyThread();

        //4.通过此对象调用start():做两件事①启动当前线程 ② 调用当前线程的run()
        t1.start();
        //问题一：我们不能通过直接调用run()的方式启动线程。
		//t1.run();

        //问题二：再启动一个线程，遍历100以内的偶数。不可以还让已经start()的线程去执行。会报IllegalThreadStateException（非法的线程状态异常）
		//t1.start();
        //我们需要重新创建一个线程的对象
        MyThread t2 = new MyThread();
        t2.start();


        //如下操作仍然是在main线程中执行的。main线程加thread1线程有两个线程同时执行
        for (int i = 0; i < 100; i++) {
            if(i % 2 == 0){
                System.out.println(Thread.currentThread().getName() + ":" + i + "***********main()************");
            }
        }
    }

}
```

<u>如果用方法一，但是线程用一次后不再用了，**可以用匿名子类的方法调用start()**</u>

```java 
//练习：创建两个分线程，其中一个线程遍历100以内的偶数，另一个线程遍历100以内的奇数
public class ThreadDemo {
    public static void main(String[] args) {
//        MyThread1 m1 = new MyThread1();
//        MyThread2 m2 = new MyThread2();
//
//        m1.start();
//        m2.start();

        //创建Thread类的匿名子类的方式
        new Thread(){
            @Override
            public void run() {
                for (int i = 0; i < 100; i++) {
                    if(i % 2 == 0){
                        System.out.println(Thread.currentThread().getName() + ":" + i);

                    }
                }
            }
        }.start();


        new Thread(){
            @Override
            public void run() {
                for (int i = 0; i < 100; i++) {
                    if(i % 2 != 0){
                        System.out.println(Thread.currentThread().getName() + ":" + i);

                    }
                }
            }
        }.start();

    }
}
```



#### Thread类的常用方法

**测试Thread中的常用方法：**

 * start():启动当前线程；调用当前线程的run()
 * run(): 通常需要重写Thread类中的此方法，将创建的线程要执行的操作声明在此方法中
 * currentThread():静态方法，返回执行当前代码的线程
 * getName():获取当前线程的名字
 * setName():设置当前线程的名字
 * yield():释放当前cpu的执行权
 * join():在线程a中调用线程b的join(),此时线程a就进入阻塞状态，直到线程b完全执行完以后，线程a才结束阻塞状态。
 * stop():已过时。当执行此方法时，强制结束当前线程。
 * sleep(long millitime):让当前线程“睡眠”指定的millitime毫秒。在指定的millitime毫秒时间内，当前线程是阻塞状态。
 * isAlive():判断当前线程是否存活



#### 线程优先级设置

1、MAX_PRIORITY：10

​      MIN _PRIORITY：1

​      NORM_PRIORITY：5  -->默认优先级



2、如何获取和设置当前线程的优先级：

​      getPriority():获取线程的优先级

​      setPriority(int p):设置线程的优先级



**说明：**<u>高优先级的线程要抢占低优先级线程cpu的执行权。但是只是从概率上讲，高优先级的线程高概率的情况下被执行。并不意味着只有当高优先级的线程执行完以后，低优先级的线程才执行。</u>

**例子：**

```java

class Thread01 extends Thread{

    @Override
    public void run() {
        for (int i=0;i<100;i++){
            if (i % 2 == 0){
//                try {
//                    sleep(1000);
//                } catch (InterruptedException e) {
//                    e.printStackTrace();
//                }
                System.out.println(Thread.currentThread().getName()+":"+i+":"+getPriority());
            }
        }
    }

    public Thread01(String name) {
        super(name);
    }
}

public class ThreadMethodTest {
    public static void main(String[] args) throws InterruptedException {
        Thread01 t1 = new Thread01("线程01");

        t1.setPriority(Thread.MAX_PRIORITY);
        t1.start();

        Thread.currentThread().setName("主线程");

        Thread.currentThread().setPriority(Thread.MIN_PRIORITY);
        for (int i=0;i<100;i++){
            if (i % 2 != 0){
                System.out.println(Thread.currentThread().getName()+":"+i+":"+Thread.currentThread().getPriority());
            }

//            if (i == 20){t1.join();}
        }
    }
}

```



 **创建多线程方法二：实现于Runnable接口**

1. 创建一个实现了Runnable接口的类
2. 实现类去实现Runnable中的抽象方法：run()
3. 创建实现类的对象
4. 将此对象作为参数传递到Thread类的构造器中，创建Thread类的对象
5. 通过Thread类的对象调用start()

**例子：**

```java
//创建一个实现了Runnable接口的类
class MThread implements Runnable {
    @Override
    //实现类去实现Runnable中的抽象方法：run()
    public void run() {
        for (int i = 0; i < 100; i++) {
            if (i % 2 != 0) {
                System.out.println(Thread.currentThread().getName() + ":" + i + ":" + Thread.currentThread().getPriority());
            }
        }
    }
}
public class ThreadTest1 {
    public static void main(String[] args) {
        //创建实现类的对象
        MThread mThread = new MThread();
        //将此对象作为参数传递到Thread类的构造器中，创建Thread类的对象
        Thread t1 = new Thread(mThread);
        //通过Thread类的对象调用start():① 启动线程 ②调用当前线程的run()-->调用了Runnable类型的target的run()
        t1.start();
        /*再创建一个线程*/
        Thread t2 = new Thread(mThread);
        t2.start();
    }
}
```



**比较创建线程的两种方式。**

 * **开发中：优先选择**：实现Runnable接口的方式
 * **原因**：1. 实现的方式没有类的单继承性的局限性

 * 2. 实现的方式更适合来处理多个线程有共享数据的情况。
    
    

 * **联系**：public class Thread implements Runnable，Thread类实现于Runnable接口

 * **相同点**：两种方式都需要重写run(),将线程要执行的逻辑声明在run()中。



#### 多线程生命周期

![多线生命周期](..\image\java高级\多线程\多线生命周期.jpg)

#### **多线程的线程安全问题**

**有什么问题**

- 多个线程执行的不确定性引起执行结果的不稳定 
- 多个线程对账本的共享，会造成操作的不完整性，会破坏数据。 

**多线程出现了安全问题** 

**问题的原因：** 

当多条语句在操作同一个线程**共享数据**时，一个线程对多条语句只**执行了一部分**，还没有 

执行完，**另一个线程参与进来**执行。导致**共享数据的错误**。 

 **解决办法：** 

对多条操作共享数据的语句，**只能让一个线程都执行完**，在执行过程中，其他线程不可以 

参与执行





### **Synchronized的使用方法<同步机制>**

<u>Java对于多线程的安全问题提供了专业的解决方式：</u>**同步机制**

#### 解决线程安全方法一：同步代码块：

```java
synchronized (对象){

// 需要被同步的代码；

}
```

**说明：**

- 操作共享数据的代码，即为需要被同步的代码。  -->不能包含代码多了，也不能包含代码少了。
- 共享数据：多个线程共同操作的变量。比如：ticket就是共享数据。
- 同步监视器，俗称：锁。任何一个类的对象，都可以充当锁。

​	**要求：多个线程必须要共用同一把锁。**

​	**补充：**在实现Runnable接口创建多线程的方式中，我们可以考虑使用this充当同步监视器。



**以下为实现Runnable接口的同步代码块使用**

```java
class Window1 implements Runnable{

    private int ticket = 100;
//    Object obj = new Object();
//    Dog dog = new Dog();
    @Override
    public void run() {
//        Object obj = new Object();错误的，锁必须是唯一的
        while(true){
            synchronized (this){//此时的this:唯一的Window1的对象   //方式二：synchronized (dog) {
                if (ticket > 0) {

                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                    System.out.println(Thread.currentThread().getName() + ":卖票，票号为：" + ticket);


                    ticket--;
                } else {
                    break;
                }
            }
        }
    }
}


public class WindowTest1 {
    public static void main(String[] args) {
        Window1 w = new Window1();

        Thread t1 = new Thread(w);
        Thread t2 = new Thread(w);
        Thread t3 = new Thread(w);

        t1.setName("窗口1");
        t2.setName("窗口2");
        t3.setName("窗口3");

        t1.start();
        t2.start();
        t3.start();
    }

}
```

**以下是继承Thread类的同步代码块的使用**

```java
class Window2 extends Thread{


    private static int ticket = 100;

    private static Object obj = new Object();

    @Override
    public void run() {

        while(true){
            //正确的
//            synchronized (obj){
            synchronized (Window2.class){//Class clazz = Window2.class,Window2.class只会加载一次
                //错误的方式：this代表着t1,t2,t3三个对象
//              synchronized (this){

                if(ticket > 0){

                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                    System.out.println(getName() + "：卖票，票号为：" + ticket);
                    ticket--;
                }else{
                    break;
                }
            }

        }

    }
}


public class WindowTest2 {
    public static void main(String[] args) {
        Window2 t1 = new Window2();
        Window2 t2 = new Window2();
        Window2 t3 = new Window2();


        t1.setName("窗口1");
        t2.setName("窗口2");
        t3.setName("窗口3");

        t1.start();
        t2.start();
        t3.start();

    }
}
```

**两种实现的不同在于：**

- 实现Runnable接口使用synchronized代码块时的锁能使用this当前对象，或者创建对象obj自然就是共享资源，obj不加static
- 继承Thread类使用synchronized代码块时的锁不能使用this当前对象，原因在于不同线程的当前对象不相同，且创建对象obj不加static不能成为共享资源





#### 解决线程安全方法二：**synchronized还可以放在方法声明中，表示整个方法为同步方法。**

**例如：**

```java
public synchronized void show (String name){ 

…

}
```

**以下为实现Runnable接口的同步方法使用，截取**

```java
class Window3 implements Runnable {

    private int ticket = 100;

    @Override
    public void run() {
        while (true) {

            show();
        }
    }

    private synchronized void show(){//同步监视器：this
        //synchronized (this){

            if (ticket > 0) {

                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

                System.out.println(Thread.currentThread().getName() + ":卖票，票号为：" + ticket);

                ticket--;
            }
        //}
    }
}

```

**以下是继承Thread类的同步方法的使用**

```java
class Window4 extends Thread {


    private static int ticket = 100;

    @Override
    public void run() {

        while (true) {

            show();
        }

    }
    private static synchronized void show(){//同步监视器：Window4.class
        //private synchronized void show(){ //同步监视器：t1,t2,t3。此种解决方式是错误的
        if (ticket > 0) {

            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            System.out.println(Thread.currentThread().getName() + "：卖票，票号为：" + ticket);
            ticket--;
        }
    }
}
```

**同步方法的两种实现的不同：**

- 实现Runnable时，使用synchronized修饰方法默认的锁就是this
- 继承Thread时，使用synchronized修饰方法莫容的锁时this，但是这是行不通的，没解决线程安全，给该方法加上static修饰符变成静态方法才能解决。



**解决懒汉式的线程安全问题**

```Java
class Bank{

    private Bank(){}

    private static Bank instance = null;

    public static Bank getInstance(){
        //方式一：效率稍差
//        synchronized (Bank.class) {
//            if(instance == null){
//
//                instance = new Bank();
//            }
//            return instance;
//        }
        //方式二：效率更高
        if(instance == null){

            synchronized (Bank.class) {
                if(instance == null){

                    instance = new Bank();
                }

            }
        }
        return instance;
    }

}
```

**切记：**

- 范围太小：没锁住所有有安全问题的代码 
- 范围太大：没发挥多线程的功能。范围



**线程的死锁问题**

- **死锁的理解：**不同的线程分别占用对方需要的同步资源不放弃，都在等待对方放弃 自己需要的同步资源，就形成了线程的死锁 
- 出现死锁后，不会出现异常，不会出现提示，只是所有的线程都处于阻塞状态，无法继续
- 我们使用同步时，要避免死锁的出现。

```java
public class ThreadTest {
    public static void main(String[] args) {

        StringBuffer s1 = new StringBuffer();
        StringBuffer s2 = new StringBuffer();

		//线程一
        new Thread(){
            @Override
            public void run() {

                synchronized (s1){

                    s1.append("a");
                    s2.append("1");

                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }


                    synchronized (s2){
                        s1.append("b");
                        s2.append("2");

                        System.out.println(s1);
                        System.out.println(s2);
                    }


                }

            }
        }.start();

		//线程二
        new Thread(new Runnable() {
            @Override
            public void run() {
                synchronized (s2){

                    s1.append("c");
                    s2.append("3");

                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                    synchronized (s1){
                        s1.append("d");
                        s2.append("4");

                        System.out.println(s1);
                        System.out.println(s2);
                    }


                }



            }
        }).start();


    }


}

```

**说明：**

两个线程启动后，分别握住对方嵌套里面要的锁，进入循环等待，，，自己看



#### 解决线程问题方法三：Lock锁  --- JDK5.0新增

- 从JDK 5.0开始，Java提供了更强大的线程同步机制——通过显式定义同 步锁对象来实现同步。同步锁使用Lock对象充当。 
- java.util.concurrent.locks.Lock接口是控制多个线程对共享资源进行访问的 工具。
- ReentrantLock 类实现了 Lock ，它拥有与 synchronized 相同的并发性和内存语义，在实现线程安全的控制中，比较常用的是ReentrantLock，可以显式加锁、释放锁。

**使用方法：**

```java
class A{
    private final ReentrantLock lock = new ReenTrantLock();
    public void m(){
        lock.lock();
        try{
        	//保证线程安全的代码;
        }
        finally{
        	lock.unlock(); 
        }
    }
}
```

<u>通过显示的lock.lock()和lock.unlock()，解决线程安全问题</u>

**注意一点：**当lock是用于解决继承Thread类的多个线程时，lock对象应该被static修饰

**例子：**

```java
@Override
    public void run() {
        while(true){
            try{

                //2.调用锁定方法lock()
                lock.lock();

                if(ticket > 0){

                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                    System.out.println(Thread.currentThread().getName() + "：售票，票号为：" + ticket);
                    ticket--;
                }else{
                    break;
                }
            }finally {
                //3.调用解锁方法：unlock()
                lock.unlock();
            }

        }
    }
```



**面试题synchronized 与 Lock的异同？**

 *   相同：二者都可以解决线程安全问题
 * 不同：synchronized机制在执行完相应的同步代码以后，自动的释放同步监视器

   ​			Lock需要手动的启动同步（lock()），同时结束同步也需要手动的实现（unlock()）

优先使用顺序：

​		Lock > 同步代码块（已经进入了方法体，分配了相应资源） > 同步方法（在方法体之外）

**同步机制的利弊：**

**利：**同步的方法，解决了线程的安全问题。

**弊：**操作同步代码时，只能个一个线程参与，其他线程等待。相当于是一个单线程的过程，效率低。



### 线程通信

线程通信涉及到的三个方法：
 * **wait()**:一旦执行此方法，当前线程就进入阻塞状态，并释放同步监视器。
 * **notify()**:一旦执行此方法，就会唤醒被wait的一个线程。如果有多个线程被wait，就唤醒优先级高的那个。
 * **notifyAll()**:一旦执行此方法，就会唤醒所有被wait的线程。



**说明：**

 * wait()，notify()，notifyAll()三个方法**必须使用在同步代码块或同步方法**中。
 * wait()，notify()，notifyAll()三个方法的调用者**必须是同步代码块或同步方法中的同步监视器**。否则，会出现IllegalMonitorStateException异常
 * wait()，notify()，notifyAll()三个方法是**定义在java.lang.Object类**中。



**面试题：sleep() 和 wait()的异同？**

1. **相同点**：一旦执行方法，都可以让当前线程进入阻塞状态

2. **不同点**：

   - 1）两个方法声明的位置不同：Thread类中声明sleep() , Object类中声明wait()

    *          2）调用的要求不同：sleep()可以在任何需要的场景下调用。 wait()必须使用在同步代码块或同步方法中
    *          结束阻塞方式不同：sleep()指定一个时间在时间内为阻塞状态。wait()需要其他线程调用notify()或notifyAll()方法才能结束阻塞。
    *          3）关于是否释放同步监视器：如果两个方法都使用在同步代码块或同步方法中，sleep()不会释放锁，wait()会释放锁。



**创建多线程方法三：实现于Callable接口  --- JDK 5.0新增**

- 与使用Runnable相比， Callable功能更强大些 
  - 相比run()方法，可以有返回值 
  - 方法可以抛出异常 
  - 支持泛型的返回值 
  - 需要借助FutureTask类，比如获取返回结果

- **Future接口** 
  - 可以对具体Runnable、Callable任务的执行结果进行取消、查询是否完成、获取结果等
  - FutrueTask是Futrue接口的唯一的实现类 
  - FutureTask 同时实现了Runnable, Future接口。它既可以作为Runnable被线程执行，又可以作为Future得到Callable的返回值

**例子：**

```java
//1.创建一个实现Callable的实现类
class NumThread implements Callable{
    //2.实现call方法，将此线程需要执行的操作声明在call()中
    @Override
    public Object call() throws Exception {
        int sum = 0;
        for (int i = 1; i <= 100; i++) {
            if(i % 2 == 0){
                System.out.println(i);
                sum += i;
            }
        }
        return sum;
    }
}


public class ThreadNew {
    public static void main(String[] args) {
        //3.创建Callable接口实现类的对象
        NumThread numThread = new NumThread();
        //4.将此Callable接口实现类的对象作为传递到FutureTask构造器中，创建FutureTask的对象
        FutureTask futureTask = new FutureTask(numThread);
        //5.将FutureTask的对象作为参数传递到Thread类的构造器中，创建Thread对象，并调用start()
        new Thread(futureTask).start();

        try {
            //6.获取Callable中call方法的返回值
            //get()返回值即为FutureTask构造器参数Callable实现类重写的call()的返回值。
            Object sum = futureTask.get();
            System.out.println("总和为：" + sum);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }

}
```



**创建线程的方式四：使用线程池**

**背景：**经常创建和销毁、使用量特别大的资源，比如并发情况下的线程，对性能影响很大。 

**思路：**提前创建好多个线程，放入线程池中，使用时直接获取，使用完放回池中。可以避免频繁创建销毁、实现重复利用。类似生活中的公共交 通工具。 

 **好处：** 

-  提高响应速度（减少了创建新线程的时间） 

-  降低资源消耗（重复利用线程池中线程，不需要每次都创建） 

-  便于线程管理（ThreadPoolExecuter实现类可以操作以下属性）
  -  corePoolSize：核心池的大小 
  -  maximumPoolSize：最大线程数 
  -  keepAliveTime：线程没有任务时最多保持多长时间后会终止