[Home](../../README)

# Android

## RxJava

#### 响应式编程（Reactive Programming）
在计算机中，响应式编程是一种面向数据流和变化传播的编程范式。这意味着可以在编程语言中很方便地表达静态或动态的数据流，而相关的计算模型会自动将变化的值通过数据流进行传播。响应式编程的一个关键概念是事件。事件可以被等待，可以触发过程，也可以触发其它事件。

#### 观察者模式
RxJava 有四个基本概念：Observable（可观察者，即被观察者）、Observer（观察者）、subscribe（订阅）、事件。Observable 和 Observer 通过 `subscribe()` 方法实现订阅关系，从而 Observable 可以在需要的时候发出事件来通知 Observer。

#### Observable
Observable 是 RxJava 描述的事件流，它决定什么时候触发事件以及触发怎样的事件。每个操作符，每次线程切换，每步都会新建一个 Observable 而非直接加工上一步的 Observable 返回给下一步。

#### Observer（Subscriber）
Observer 是 RxJava 的观察者，它决定了事件触发时具体的行为。Subscriber 是对 Observer 接口进行了一些扩展和实现的抽象类。

#### Scheduler
RxJava 通过 Scheduler 来指定任务执行的线程。通过 `subscribeOn()` 指定 Observable 执行的线程，通过 `observeOn()` 指定 Observer 执行的线程。默认提供的 Scheduler 有：
- **`Schedulers.immediate()`**
直接在当前线程运行，相当于不指定线程。默认的 Scheduler。
- **`Schedulers.newThread()`**
启动一个常规的新线程并在其中运行。
- **`Schedulers.io()`**
I/O 操作（读写文件、读写数据库、网络信息交互等）所使用的 Scheduler，内部实现是是用一个无数量上限的线程池，可以重用空闲的线程，因此多数情况下 io() 比 newThread() 更有效率。不要把计算工作放在 io() 中，可以避免创建不必要的线程。
- **`Schedulers.computation()`**
计算所使用的 Scheduler。这个计算指的是 CPU 密集型计算，即不会被 I/O 等操作限制性能的操作，例如图形的计算。这个 Scheduler 使用的固定的线程池，大小为 CPU 核数。不要把 I/O 操作放在 computation() 中，否则 I/O 操作的等待时间会浪费 CPU。
- **`AndroidSchedulers.mainThread()`**
在 Android 主线程运行。

#### map、flatMap、lift
`map()` 方法会把传入的参数转化之后返回另一个对象。`flatMap()` 方法则会把传入的参数转化成一个新的 Observable 之后返回。它们内部都是基于 `lift()` 方法实现的，`lift()` 是针对事件项和事件序列的变换方法。

#### compose
`compose()` 方法会对 Observable 进行一个整体变换。与 `lift()` 区分，`compose()` 方法是对 Observable 自身进行变换。
通过 RxLifecycle 可以讲 Observable 绑定生命周期。

#### doOnSubscribe
`doOnSubscribe()`、`Subscriber.onStart()` 都可以用作流程开始前的初始化，但 `onStart()` 方法不能指定线程，只能执行在 `subscribe()` 被调用时的线程，而 `doOnSubscribe()` 方法可以指定执行线程。默认情况下，`doOnSubscribe()` 执行在 `subscribe()` 发生的线程，如果在 `doOnSubscribe()` 之后有 `subscribeOn()` 的话，它将执行在离它最近的 `subscribeOn()` 所指定的线程。

#### concat
`concat()` 方法可以拼接多个 Observable，并且只有触发了 `onComplete()` 后才会开始执行下一个 Observable。

#### zip
`zip()` 方法可以合并多个 Observable 的结果并一次性返回。


[Home](../../README)