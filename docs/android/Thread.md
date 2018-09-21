[Home](../../README.md)

# Android

## Thread

#### Thread
线程是操作系统能进行运算调度的最小单元。
Android 底层调用了 Linux 的线程来执行，主要为了兼容性和效率。
线程有 Android 自己定义的优先级，MIN_PRIORITY = 1，NORM_PRIORITY = 5，MAX_PRIORITY = 10，主线程优先级为 NORM_PRIORITY。
线程同时有 Linux 定义的优先级，-20 是最高的优先级，19 是最低的优先级。***暂未找到对应关系。***
调用 `new Thread()` 建立的线程，优先级直接继承实例化它的线程的优先级，故主线程直接 `new Thread()` 时优先级和主线程一样。
后台线程过多也会导致主线程卡顿，线程调度需要消耗资源。

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


#### WorkManager


[Home](../../README.md)