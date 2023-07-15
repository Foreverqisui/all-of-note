# java并发学习（2022年6月22日15:13:21）😍

# 命运之始（2022年6月22日15:13:52）🤩

## 一、线程池--治理线程的法宝😏

### 1、线程池的自我介绍

#### 线程池的重要性

> 面试需要🤣

#### 线程池的优势

> 加快响应速度
> 
> 合理利用CPU和内存
> 
> 统一管理

#### 线程池的适用应用场合

> 服务器接收到大量请求时，可以大大减少线程的 <mark>创建和销毁次数</mark>，提高服务器的工作效率

### 2、创建和停止线程池

#### 线程池构造函数的参数

##### corePoolSize

> 指的是<mark>核心线程数</mark>：线程池在完成初始化后，默认无任何线程，线程池会等到有任务到来时再创建新的线程

##### maxPoolSize

> 额外可添加的最大线程数

##### keepAliveTime

> 如果多于corePoolSize的线程空闲时间大于keepAliveTime，则停止

##### wordQueue

> 直接交接：SynchronousQueue
> 
> 无界队列：LinkedBlockingQueue
> 
> 有界队列：ArrayBlockingQueue

##### 添加线程规则

![添加线程规则.jpg](D:\A-工作文档\学习笔记\八股笔记\java并发\并发图片\添加线程规则.jpg)

#### 线程池应该手动创建还是自动创建

> 答：根据不同的业务场景手动创建

##### 四种常见的线程池

###### FixedThreadPool

> 传进去的是LinkedBlockingQueue没有容量上限，请求会越传越多，到请求堆积时，容易占用大量的内存，导致OOM的产生

###### SingleThreadExecutor

> 同上一种，不过此种线程数设置成1

###### CachedThreadPool

> maxPoolSize被设置成Integer.MAX_VALUE
> 
> 线程数过多时也会造成OOM
> 
> 可缓存回收

###### ScheduledThreadPool

> 支持定时及周期性任务执行的线程池

###### workStealingPool（不常用）

> 有子任务
> 
> 窃取

### 3、停止线程池的正确方法

#### shutdown

#### isShutdown

#### isTerminated

> 判断所有线程是否完全停止

#### shutdownNow

> 立刻关闭掉线程池

### 4、4种拒绝策略

#### AbortPolicy

> 直接抛异常

#### DiscardPolicy

> 默默丢弃任务，不提示

#### DiscardOldestPolicy

> 丢弃最老的任务

#### CallerRunsPolicy

> 让主线程运行
> 
> 降低提交速度（负反馈）

### 5、线程池的实现原理

#### 线程池的组成部分

##### 1、线程池管理器

##### 2、工作线程

##### 3、任务队列

##### 4、任务接口

#### Executor家族

> Executor：顶层接口
> 
> ExecutorService：继承顶层接口，并增添一些管理线程池的基础方法
> 
> Executors：工具类
> 
> AbstractExecutorService：实现ExecutorService
> 
> ThreadPoolExecutor：继承AbstractExecutorService

#### 线程池实现任务复用的原理

> 相同线程执行不同任务

#### 线程池状态

> RUNNING：接受新任务并处理排队任务
> 
> SHUTDOWN：不接受新任务但处理排队任务
> 
> STOP：不接受、不处理
> 
> TIDYING：所有任务已经完成
> 
> TERMINATED

## 二、ThreadLocal详解🤨

> 让某个需要用到的对象在线程间隔离
> 
> 在任何方法中轻松获取到对象

### 两大使用场景

#### 1、每个线程需要一个独享的对象（通常是工具类）

> 每个Thread有自己的实例副本，不共享（保证线程安全）

```java
public class ThreadLocal1 {
    /**
    * 构建线程池
    * */
    public static ExecutorService thread = Executors.newFixedThreadPool(10);
    public static void main(String[] args) {
        //创建1000个任务
        for (int i = 0; i < 1000; i++) {
            int finalI = i;
            //执行任务逻辑
            thread.submit(() -> {
                //根据i进行日期格式化输出
                String date = new ThreadLocal1().date(finalI);
                System.out.println(date);
            });
        }
        thread.shutdown();
    }

    public String date(int finalI) {
        //获取日期
        Date date = new Date(1000 * finalI);
        //获取写好的TreadLocal方法
        SimpleDateFormat dateFormat = TreadSafeFormat.dateFormat.get();
        //格式化输出
        return dateFormat.format(date);

    }
}
class TreadSafeFormat {
    /**
     * 利用ThreadLocal 给每个线程分配自己的dateFormat对象 ，保证了线程安全，并且高效利用内存
     * */
    public static ThreadLocal<SimpleDateFormat> dateFormat = ThreadLocal.withInitial(() -> {
        //重写初始化方法，返回格式化后的日期
        return new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
    });
}
```

#### 2、每个线程内需要保存全局变量，避免参数麻烦

> 

```java
/**
 * @author foreverqisui
 * @date 2022年6月22日18:23:18
 * ThreadLocal使用的第二个场景
 */
public class ThreadLocal2 {
    public static void main(String[] args) {
        new Service1().process();
    }
}

class Service1 {
    public void process() {
        User user = new User("超哥");
        UserContextHolder.holder.set(user);
        new Service2().process();
    }
}

class Service2 {
    public void process() {
        User user = UserContextHolder.holder.get();
        System.out.println(user.username);
        new Service3().process();
    }
}

class Service3 {
    public void process() {
        User user = UserContextHolder.holder.get();
        System.out.println(user.username);
    }
}

class UserContextHolder {
    public static ThreadLocal<User> holder = new ThreadLocal<User>();
}

class User {
    String username;

    public User(String username) {
        this.username = username;
    }
}
```

### 常见方法

#### initialValue（）

> 初始化

#### set

> 为线程设置一个新值

#### get

> 得到线程对应的value

#### remove

> 删除对应线程的值

## 三、锁🥱

![锁的分类.jpg](D:\A-工作文档\学习笔记\八股笔记\java并发\并发图片\锁的分类.jpg)

### 1、Lock接口

> 常见实现类：ReentrantLock

#### synchronized的缺点

> 效率低：锁的释放情况少、试图获得锁时不能设定超时、不能中断一个正在试图获得锁的线程
> 
> 不够灵活：加锁和释放的时机单一，每个锁仅有单一的条件
> 
> 无法知道是否成功获取锁

#### 主要方法

##### lock()

> 普通的获取锁，如果锁被占用，则<mark>等待</mark>
> 
> 异常时不会和synchronized一样自动释放锁
> 
> 一般在finally中释放
> 
> 缺点：不能被中断，会陷入死锁

##### tryLock()

> tryLock()用来尝试获取锁，如果当前的锁没有被其他线程占用，则获取成功返回true，否则返回false，代表获取锁失败
> 
> 相较于lock（）这种锁可以根据是否能获取到锁来决定后续程序的行为
> 
> 会立即返回，不会持续等待

##### lockInterruptibly()

> 同上，，设置等待时间可无限，可以被中断

##### 可见性保证

> 

### 2、乐观锁和悲观锁

#### 悲观锁

> 悲观的认为会出问题
> 
> synchronized和lock
> 
> 使用场景：适合并发写入多大情况，适用于临界区持锁时间较长的情况
> 
> * 临界区有IO操作
> 
> * 临界区代码复杂或循环量大
> 
> * 临界区竞争非常激烈
> 
> 缺点：
> 
> * 阻塞和唤醒带来的性能劣势
> 
> * 永久阻塞：持有锁的线程陷入死锁或无限循环等，等待该线程释放锁的线程将永不会被执行
> 
> * 优先级反转

#### 乐观锁

> 乐观的认为不会出问题，等到出问题再解决
> 
> 原子类和并发容器和提交git
> 
> 使用场景：适合并发写入少，大部分是读取的

### 3、可重入锁和非可重入锁

> 可重入：同一个线程可重复使用一把锁
> 
> * 避免死锁
> 
> * 提升封装性

#### ReentrantLock（AQS）

```java
/**
 * @author foreverqisui
 * @date 2022年6月23日11:18:17
 * 借助ReentrantLock演示多线程预定电影票
 */
public class ThreadReentrantLock {
    /**构建ReentrantLock锁*/
    private static ReentrantLock lock = new ReentrantLock();
    /**线程池*/
    public static ExecutorService thread = Executors.newFixedThreadPool(4);
    /**购票方法*/
    private static void bookSeat() {
        //上锁
        lock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + "预定电影座位");
            Thread.sleep(100);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
            System.out.println(Thread.currentThread().getName()+"预定完成");
        }
    }

    public static void main(String[] args) {
        thread.submit(ThreadReentrantLock::bookSeat);
        thread.submit(ThreadReentrantLock::bookSeat);
        thread.submit(ThreadReentrantLock::bookSeat);
        thread.submit(ThreadReentrantLock::bookSeat);
    }
}
```

### 4、公平锁和非公平锁

### 5、共享锁和排它锁

> 排它锁（写锁）：只允许一个线程写
> 
> 共享锁（读锁）：允许多个线程读，无法修改删除数据，只读

```java
/**
 * @Author foreverqisui
 * @Date 2022/6/23 15:46
 * @PackageName:com.pc.thread
 * @ClassName: ThreadReentrantReadWriteLock
 * @Description: 用来学习读写锁
 */
public class ThreadReentrantReadWriteLock {
    /**构建读写锁*/
    private static final ReentrantReadWriteLock lock = new ReentrantReadWriteLock();
    /**构建读锁*/
    private static ReentrantReadWriteLock.ReadLock readLock = lock.readLock();
    /**构建写锁*/
    private static ReentrantReadWriteLock.WriteLock writeLock = lock.writeLock();
    /**线程池*/
    public static ExecutorService thread = Executors.newFixedThreadPool(4);
    /**
     * 构建读写方法 根据读写锁
     */
    private static void read(){
        readLock.lock();
        try {
            System.out.println("得到了读锁");
            Thread.sleep(1000);
        }catch (InterruptedException e) {
            e.printStackTrace();
        }finally {
            readLock.unlock();
            System.out.println("释放读锁");
        }
    }
    /**
     * 构建读写方法 根据读写锁
     */
    private static void write(){
        writeLock.lock();
        try {
            System.out.println("得到了写锁");
            Thread.sleep(1000);
        }catch (InterruptedException e) {
            e.printStackTrace();
        }finally {
            writeLock.unlock();
            System.out.println("释放写锁");
        }
    }

    public static void main(String[] args) {
        thread.submit(ThreadReentrantReadWriteLock::read);
        thread.submit(ThreadReentrantReadWriteLock::read);
        thread.submit(ThreadReentrantReadWriteLock::write);
        thread.submit(ThreadReentrantReadWriteLock::write);
    }
}
```

#### 读锁插队策略

> * 公平锁：不允许插队，提升效率但容易造成饥饿
> 
> * 不公平锁：读锁不可以插队（当队列头为写时），避免饥饿

#### 锁的升降级

> 支持锁的降级（写->读），不支持升级

### 6、自旋锁和阻塞所

### 7、可中断锁

## 四、原子类

![原子类六大类型.jpg](D:\A-工作文档\学习笔记\八股笔记\java并发\并发图片\原子类六大类型.jpg)

> 作用
> 
> * 不可分割
> 
> * 一个操作不可中断
> 
> * 保证线程安全
> 
> * 粒度更细：同锁比
> 
> * 效率更高：同锁比

## 五、CAS原理

> 并发进行时，判断旧值是否更改过
> 
> * 如果旧值==期待值，则CAS成功更改成新值
> 
> * 否则失败，返回当前值
> 
> 缺点
> 
> * ABA问题：添加版本号
> 
> * 自旋过多

### 应用场景

> 乐观锁、并发容器、原子类

## 六、并发容器

> 
