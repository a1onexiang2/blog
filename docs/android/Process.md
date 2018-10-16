[Home](../../README.md)

# Android

## Process
进程按重要性可以分为五类：
- 前台进程 (Foreground process)
- 可见进程 (Visible process)
- 服务进程 (Service process)
- 后台进程 (Background process)
- 空进程 (Empty process)

Android 应用程序框架层创建的应用程序进程具有两个特点，一是进程的入口函数是 ActivityThread.main，二是进程天然支持 Binder 进程间通信机制；这两个特点都是在进程的初始化过程中实现的。
AMS（ActivityMagagerService）启动进程是从其成员函数 `startProcessLocked()` 开始调用 `Process.start()` 方法开始的。具体流程如下：
![image](https://user-images.githubusercontent.com/8423120/46992275-d7499700-d13b-11e8-96f4-2cdd8b1ee0d5.png)


#### Binder
![image](https://user-images.githubusercontent.com/8423120/46243271-0bffe380-c405-11e8-8a30-2d4d58fc056c.png)
![image](https://user-images.githubusercontent.com/8423120/46243215-41f09800-c404-11e8-8fe6-862551b06dc7.png)

#### Bundle
四大组件中的三大组件（Activity、Service、BoardcastReceiver）都支持在 Intent 中传递 Bundle 数据的，Bundle 实现了 Parcelable 接口，可以方便地在不同的进程间传输。
在一个进程中启动了另一个进程的组件，可以在 Bundle 中附加需要传输的信息并通过 Intent 发送。其中传输的数据必须是能够被序列化的，比如基本类型，实现了 Parcelable 接口的对象、实现了 Serializable 接口的对象以及一些 Android 支持的特殊对象。
Bundle 方式只支持单方面的传输数据，不支持互相通讯。

#### 文件共享
两个进程通过读/写同一个文件来交换数据。通过文件除了可以交换一些文本信息外，我们还可以序列化一个对象到文本系统中的同时从另一个进程中反序列化这个对象。
Android 基于 Linux，并发读/写文件没有任何限制。使用文件共享模式需要考虑进程间同步问题，以及读写效率问题。

#### Messenger
通过 Messenger 可以在不同进程中传递 Message 对象，直接在 Message 中放入需要传递的数据即可。Messenger 是一种轻量级的 IPC 方案，它的底层实现是 AIDL。
Messenger 的请求队列是串行的，无法并发执行，因此在服务端不用考虑线程同步问题。

#### AIDL
Messenger 是串行的方式处理客户端发来的消息，如果大量的并发请求，那么用 Messenger 就不太合适了。同时，Messenger 的做用户主要是为了传递消息，很多时候我们需要跨进程调用服务端的方法，这种情形用 Messenger 就无法做到了，但是我们可以使用 AIDL 来实现跨进程的方法调用，AIDL 也是 Messenger 的底层实现，因此，Messenger 本质上也是 AIDL，只不过系统为我们做了封装从而方便上层的调用而已。
首先创建一个 Service 和 AIDL 接口，接着创建一个类继承 AIDL 接口中的 Stub 类并实现 Stub 类的中的抽象方法，在 Service 的 `onBind()` 方法中返回这个类的对象，然后客户端就可以绑定服务端的 Service，建立连接后就可以访问远程服务端的方法了。

#### ContentProvider
ContentProvider 是 Android 提供的专门用于不同应用间进行数据共享的方式。ContentProvider 的底层实现是 Binder。
系统预置了许多 ContentProvider，比如通信录信息、日程表信息等，要跨进程访问这些信息，只需要通过 ContentResolver 的 `query()`、`update()`、`insert()` 和 `delete()` 方法即可。
创建自定义的 ContentProvider 只需继承 ContentProvider 类并实现六个抽象方法：`onCreate()`、`query()`、`update()`、`insert()`、`delete() `和 `getType()`。`onCreate()` 代表 ContentProvider 的创建，`getType()` 用来返回一个 Uri 请求所对应的 MIME 类型（媒体类型），比如图片、视频等，如果应用不关注这个选项，可以直接在这个方法中返回 null 或者 `"/"`；剩下的四个方法对应于 CRUD 操作，即增删改查。这六个方法均运行在 ContentProvider 的进程中，除了 `onCreate()` 由系统回调并运行在主线程里，其他五个方法均由外界回调并运行在 Binder 线程池中。

#### Socket
Socket 也称 “套接字” ，它分为流式套接字和用户数据包套接字，分别对应于网络的传输控制层中的 TCP 和 UDP。TCP 协议是面向连接的协议，提供稳定的双向通信功能，TCP 本身提供了超时重传机制，因此具有很高的稳定性；而 UDP 是无连接的，提供不稳定的单向通信功能，当然 UDP 也可以实现双向通信功能。在性能上，UDP 具有更好的效率，其缺点是不保证数据一定能够正确传输，尤其是在网络拥塞的情况下。
实际上通过 Socket 不仅仅能实现进程间的通信，还可以实现设备间的通信，当然前提是这些设备之间的 IP 地址互相可见。

#### Binder 连接池
每个业务模块创建自己的 AIDL 接口并实现此接口，这个时候不同业务模块之间是不能有耦合的，并向服务端提供自己的唯一标识和其对应的 Binder 对象。对于服务端来说，只需要实现一个 Service 就够了，服务端提供一个 queryBinder 接口，这个接口能够根据业务模块的特征来返回相应的 Binder 对象给它们，不同的业务模块拿到不同的 Binder 对象后就可以进行远程方法调用了。
Binder 连接池的主要作用就是将每个业务模块的 Binder 请求统一转发到远程 Service 中执行，从而避免重复创建 Service 的过程。

#### PendingIntent
PendingIntent 不是跨进程通讯，它是 Android 提供的一种用于外部程序调起自身程序的能力，生命周期不与主程序相关，它的核心可以汇总成四个字 “异步激发” 。
PendingIntent 是系统对于待处理数据的一个引用，称之为 token。当主程序被 Killed 时，token 还是会继续存在的，可以继续供其他进程使用。调用 `PendingIntent.cancel()` 方法才可以取消 PendingIntent。

[Home](../../README.md)
