[Home](../../README.md)

# Android

## Thread

线程是操作系统能进行运算调度的最小单元。
Android 底层调用了 Linux 的线程来执行，主要为了兼容性和效率。
线程有 Android 自己定义的优先级，MIN_PRIORITY = 1，NORM_PRIORITY = 5，MAX_PRIORITY = 10，主线程优先级为 NORM_PRIORITY。
线程同时有 Linux 定义的优先级，-20 是最高的优先级，19 是最低的优先级。***暂未找到对应关系。***
调用 `new Thread()` 建立的线程，优先级直接继承实例化它的线程的优先级，故主线程直接 `new Thread()` 时优先级和主线程一样。
后台线程过多也会导致主线程卡顿，线程调度需要消耗资源。
线程数上限与 Linux 一样，为 15193。
`interrupt()` 方法不能中断正在运行过程中的线程，只能中断阻塞过程中的线程。

#### 锁的分类
- **可重入锁**
在一个方法获取锁后，调用了其它的同步方法，可以不用重新申请锁对象直接执行。
- **可中断锁**
synchronized 不可中断，Lock 可中断。
- **公平锁**
公平锁会根据请求的顺序来获取锁，当锁被释放时，等待时间最长的线程会被执行。
synchronized 是非公平锁，无法保证等待线程的执行顺序。
ReentrantLock、ReentrantReadWriteLock 默认情况下是非公平锁，可以通过构造方法来设置是否使用公平锁。
- **读写锁**
读写锁将对一个资源（比如文件）的访问分成两个锁，一个读锁和一个写锁。读写锁之间有相互影响的逻辑。

#### 线程安全
- **synchronized**
保证代码的可见性、原子性和顺序性。synchronized 是 Java 的内置的关键字，是内置特性。
被修饰的代码块只有在执行完毕或发生异常时才会释放锁。
不需要手动释放锁，但也不可以获取状态、中断操作等，扩展性较差。
使用非静态同步方法时，锁住的是当前实例；使用静态同步方法时，锁住的是该类的 Class 对象。
- **Lock**
Lock 是一个 Java 接口。
使用 Lock 必须手动释放锁，一般在 try/catch/finally 代码块中调用，并在 finally 中释放锁。
`lock()` 方法可以获取锁，如果获取锁失败会阻塞等待；
`tryLock()` 方法可以尝试获取锁，并会返回获取锁的结果，如果失败直接返回 false，不会等待；
`lockInterruptibly()` 方法可以尝试获取锁，如果获取锁失败会阻塞等待，被调用 `Thread.interrupt()` 时会中断等待锁状态并抛出 `InterruptedException`。
    - **ReentrantLock**
    ReentrantLock 是唯一实现 Lock 的类。意思是“可重入锁”。
    - **ReadWriteLock**
    ReadWriteLock 也是一个 Java 接口。将读写操作分开，分成两个锁来分配给线程，使多线程可以同时进行读写操作。
    - **ReentrantReadWriteLock**
    ReentrantReadWriteLock 是唯一实现 ReadWriteLock 的类。
    读锁之间不会相互阻塞。当需要获取写锁时，会等待读锁释放；当写锁被占用时，读和写都需要等待写锁释放。
    - **Condition**
    Condition 对象通过 `lock.newCondition()` 来创建，用 `await()` 让线程等待，用 `signal()` 唤醒线程。
- **volatile**
Java 提供了volatile关键字来保证可见性。当使用 volatile 修饰某个变量时，它会保证对该变量的修改会立即被更新到内存中，并且将其它缓存中对该变量的缓存设置成无效，因此其它线程需要读取该值时必须从主内存中读取，从而得到最新的值。
synchronized 和锁需要通过操作系统来仲裁谁获得锁，开销比较高，而 volatile 开销小很多。因此在只需要保证可见性的条件下，使用 volatile 的性能要比使用锁和 synchronized 高得多。
volatile 不能保证程序的原子性。
volatile 在一定程度上可以保证顺序性。volatile 修饰的变量被调用时，可以保证之前的代码全都运行过，之后的代码还未运行，但是之前或之后的代码的执行顺序依旧不确定。

#### 线程通讯
- **CountDownLatch**
倒计数器，调用线程等待多个线程完成后唤醒。
每次调用 `countDown()` 方法，计数减 1；`await()` 会等待直到计数归 0；若被中断则抛出 InterruptedException。
- **CyclicBarrier**
循环屏障，多个线程在调用线程完成后唤醒。
每次调用 `await()` 都会等待，计数减 1；`await()` 的调用导致计数归 0 时所有等待立即被唤醒。
- **Phaser**
Phaser 比较适合这样一种场景：一种任务可以分为多个阶段，现希望多个线程去处理该批任务，对于每个阶段，多个线程可以并发进行，但是希望保证只有前面一个阶段的任务完成之后才能开始后面的任务。
`arriveAndAwaitAdvance()` 会等待进入下个阶段。`arriveAndDeregister()` 会立刻返回并让其它线程需要等待的数量减 1。

#### ThreadLocal
ThreadLocal 并不解决线程间共享数据的问题。同一个 ThreadLocal 所包含的对象在不同的 Thread 中有不同的副本。
ThreadLocal 通过隐式的在不同线程内创建独立实例副本避免了实例线程安全的问题。
每个线程持有一个 Map 并维护了 ThreadLocal 对象与具体实例的映射，该 Map 由于只被持有它的线程访问，故不存在线程安全以及锁的问题。
ThreadLocalMap 的 Entry 对 ThreadLocal 的引用为弱引用，避免了 ThreadLocal 对象无法被回收的问题。
ThreadLocalMap 的 set 方法通过调用 replaceStaleEntry 方法回收键为 null 的 Entry 对象的值（即为具体实例）以及 Entry 对象本身从而防止内存泄漏。
ThreadLocal 适用于变量在线程间隔离且在方法间共享的场景。

#### 为什么 Android 不允许在主线程以外的线程操作 UI？
Android UI 为了高效率是线程不安全的。在不同线程处理 UI 会导致一些不可预知的结果，整个过程不可控。权衡之下，限制了只有主线程可以访问 UI。

#### 为什么 Android 不允许在主线程进行网络通讯？
Android 所有的 UI 与用户交互都是在主线程执行的。网络通讯的过程是不可预知的，极有可能会阻塞主线程，从而导致用户无法操作。

#### Runnable
Thread 需要传入或复写 Runnable，并在启动时调用 Runnable 接口中的方法来执行任务。
不能直接调用 `Thread.run()` 方法，该方法并没有线程创建或切换的操作。`Thread.start()` 方法才是正确启动线程的方法。

#### Callable
Callable 类似于 Runnable。但是任务执行后可返回值，而 Runnable 不能返回值。
`Callable.call()` 方法可以抛出异常，`Runnable.run()` 方法不可以。
运行 Callable 任务可以拿到一个 Future 对象，表示异步计算的结果。它提供了检查计算是否完成的方法，以等待计算的完成，并检索计算的结果。
通过 Future 可以了解任务执行情况，可取消任务的执行，可获取执行结果。

#### FutureTask
FutureTask 实现了 Runnable 和 Callable，既可以在 Thread 中使用，又可以在 ExecutorService 中使用。
FutureTask 弥补了 Thread 的不足，是一种可以取消的异步的计算任务，并且有三个状态：等待、运行和完成。完成包括所有计算以任意的方式结束，包括正常结束、取消和异常。

#### Handler、Looper、Message、MessageQueue
![](https://pic2.zhimg.com/80/28a5f0d87457d432727270313cfec3a9_hd.png)
Looper 是一个消息泵，不断地遍历 MessageQueue，从中读取 Message 并交给 Handler 处理。
底层使用了管道的方式，发送消息时会向管道写入数据，管道会唤醒其它线程来处理消息。
`new Handler()` 会直接在调用的线程建立 Handler。若要指定线程，需要传入指定线程的 Looper，通过 `Looper.myLooper()` 来获取。
Thread 的构造函数中调用 `Looper.myLooper()` 只会得到主线程的 Looper，因为此时线程还未构建好。

#### AsyncTask
AsyncTask 是一个轻量级的异步任务类。每个 AsyncTask 只能运行一次，无论正常完成、被取消或中断。Linux 优先级是 Process.THREAD_PRIORITY_BACKGROUND = 10。
AsyncTask 内部有一个线程池，核心线程池大小是 `Math.max(2, Math.min(CPU_COUNT - 1, 4))`，最大线程池大小是 `CPU_COUNT * 2 + 1`。
`doInBackground()` 方法运行在工作线程中，`onPreExecute()`、`onPostExecute()` 方法运行在主线程中。通过 Handler 来通讯

#### ExecutorService
线程池接口，ThreadPoolExecutor 是它的基础实现。
通过 ThreadPoolExecutor 实现了四类常用线程池，分别是：FixedThreadPool、CachedThreadPool、ScheduledThreadPool、SingleThreadExecutor。
- **FixedThreadPool**
通过 `Executors.newFixedThreadPool()` 方法创建。
线程数量固定，且全部为核心线程。
没有超时机制且排队任务队列无限制。
响应较快，不用担心线程会被回收。
- **CachedThreadPool**
通过 `Executors.newCachedThreadPool()` 方法创建。
它是一个数量无限多的线程池，且全部为非核心线程，
若有新任务但没有空闲线程时，不会等待，而是会直接创建新的线程执行。
超时时间是 60 秒。理论上该线程池不会有占用系统资源的无用线程。
此线程池适合执行大量耗时小的任务。
- **ScheduledThreadPool**
通过 `Executors.newScheduledThreadPool()` 方法创建。
有数量固定的核心线程，且有数量无限多的非核心线程。
非核心线程超时时间是 10 秒。
这类线程池适合用于执行定时任务和固定周期的重复任务。
- **SingleThreadExecutor**
通过 `Executors.newSingleThreadExecutor()` 方法创建。
它内部只有一个核心线程，它确保所有任务都按顺序执行。
超时时间是 0 秒。
统一所有的外界任务到同一线程中，可以忽略线程同步问题。

#### HandlerThread
HandlerThread 继承自 Thread，封装了 Handler 与 Looper的逻辑。通过调用 `getThreadHandler()` 方法可以获得 Handler 并直接使用。

#### IntentService
IntentService 继承自 Service，封装了 HandlerThread 的逻辑。可以直接执行一些异步任务。

#### JobScheduler
但是它只在 Lollipop 以上的系统上可用，而且存在一个重大 bug，在 Marshmallow 之后才可以正常使用。
JobScheduler 依赖于 Google Play Service。
可以尝试使用 JobIntentService，它在 Oreo 之前的版本会使用 Service，在 Oreo 之后的版本会使用 JobScheduler。
JobScheduler 首先会调度一个任务，然后在合适的时机（比如说延迟若干时间之后，或者等手机空闲了）系统会开启你的 MyJobService，然后执行 onStartJob() 里的处理逻辑。

#### WorkManager
WorkManager 架构组件是用来管理后台工作任务。
WorkManager 会根据你的设备情况，自动选用 JobScheduler, 或是 AlarmManager 来实现后台任务。
WorkManager 组件库里提供了一个专门做周期性任务的类 PeriodicWorkRequest，最小的周期时间是 15 分钟。


[Home](../../README.md)