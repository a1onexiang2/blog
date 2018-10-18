[Home](../../README.md)

# Android

## Improvement

#### 应用启动速度
1. 利用主题快速显示界面
    该方法没有在实际上加快应用启动速度，但是在交互上会给用户更好的体验。
2. 不要在启动时初始化过多内容
    在 `Application.onCreate()` 中不要执行耗时任务阻塞主线程。可以从以下四个方面进行优化：
    - 同步初始化重要组件
    把热更新、MultiDex 等影响整个应用的库需要最先同步初始化；把 EventBus、Router、Log 等立刻需要使用到的库放在 Application 中同步初始化。
    - 异步初始化组件
    把用户行为统计、异常统计相关的库放在异步线程中初始化。
    - 延迟初始化组件
    把 Image、Network、Database、JSON 等业务需要的库放在首屏 Activity 中同步初始化。
    - 不要执行耗时任务
    一些耗时的全局变量可以延迟创建。
3. 正确使用线程
    线程如果没有设置正确的优先级，会影响到资源争抢，造成主线程卡顿。
4. 去掉无用代码、重复逻辑
    具体情况具体分析，可以考虑合并逻辑、合并请求、适当缓存等。

#### 布局优化
大多数的 Android 设备是按照 60fps 来刷新屏幕的。因此，我们需要确保在 16ms(1000/60Hz) 内完成单次刷新的操作（包括 `measure()`、`layout()`、`draw()`），这也是 Android 系统每隔 16ms 就会发出一次 VSYNC 信号触发对 UI 进行渲染的原因。如果整个过程在 16ms 内顺利完成则可以展示出流畅的画面；然而由于任何原因导致接收到 VSYNC 信号的时候无法完成本次刷新操作，就会产生掉帧的现象。
可以从以下四个方面进行优化：
1. 减少嵌套层次
    ChildView 的位置受 ParentView 的影响，类如 RelativeLayout、LinearLayout 等经常需要执行两次 `measure()` 才能完成测量，嵌套、相互嵌套、深层嵌套等会使 `measure()` 调用次数呈指数级增长。
2. 减少 View 数量
    Android 的布局文件的加载是 LayoutInflater 通过 XmlPullParser 来解析 xml 文件，并通过反射创建 View。View 的数量会影响到内存占用和绘制效率。
3. 利用 merge 标签
    merge 可以用来合并布局，减少布局的层级。merge 多用于替换顶层 FrameLayout 或者 include 布局时, 用于消除因为引用布局导致的多余嵌套。
4. 利用 ViewStub
    ViewStub 可以延迟 View 的加载，避免一些次要的 View 争抢资源。
可以利用下列工具来进行检查：
- 调试 GPU 过度绘制
可以在手机上 `设置→开发者选项→调试 GPU 过度绘制→显示过度绘制区域` 打开显示过度绘制区域选项。
- UiAutoMatorViewer
SDK 工具中使用 UiAutoMatorViewer 分析布局层次。
- HierarchyViewer
SDK 工具中使用 HierarchyViewer 可以可视化展示布局结构，分析 View 总数、分布、无用布局数量等，并统计页面的 `measure()`、`layout()`、`draw()` 消耗的时间。
- Profiling GPU Rendering
可以在手机上 `设置→开发者选项→GPU 呈现模式分析` 设置显示为条形图来查看 GPU 工作的具体情况。

#### 内存优化
- **共享内存\*\*\r\r
Android 系统通过下面几种方式来实现共享内存：
Android 应用的进程都是从 Zygote 的进程 fork 出来的。Zygote 进程在系统启动并且载入通用的 framework 的代码与资源之后开始启动。为了启动一个新的程序进程，系统会 fork Zygote 进程生成一个新的进程，然后在新的进程中加载并运行应用程序的代码。这使得大多数的 RAM pages 被用来分配给 framework 的代码，同时使得 RAM 资源能够在应用的所有进程之间进行共享。
大多数 static 的数据被 mmapped 到一个进程中。这不仅仅使得同样的数据能够在进程间进行共享，而且使得它能够在需要的时候被 paged out。常见的 static 数据包括 Dalvik Code，app resources，so 文件等。
大多数情况下，Android 通过显式的分配共享内存区域 (例如 ashmem 或者 gralloc) 来实现动态 RAM 区域能够在不同进程之间进行共享的机制。例如，Window Surface 在 App 与 Screen Compositor 之间使用共享的内存，Cursor Buffers 在 ContentProvider 与 Clients 之间共享内存。
- **Android Runtime 内存划分\*\*\r\r
![image](https://user-images.githubusercontent.com/8423120/46125448-b4724400-c25b-11e8-87e6-7701fcbdbf25.png)
- **Memory Leak\*\*\r\r
用动态存储分配函数动态开辟的空间，在使用完毕后未释放，结果导致一直占据该内存单元。直到程序结束。即所谓的内存泄漏。
内存泄露就是系统回收不了那些分配出去但是又不使用的内存, 随着程序的运行, 可以使用的内存就会越来越少。以下情况可能会出现内存泄漏：
    1. 非静态内部类的静态实例
        非静态内部类会维持一个外部类实例的引用，如果非静态内部类的实例是静态的，就会间接长期维持着外部类的引用，阻止被回收掉。如果需要使用内部类，可以使用静态内部类或使用 WeakReference 保持弱引用。
    2. 匿名类
        相似地，匿名类也维护了外部类的引用。
    3. 资源对象未关闭
        资源性对象如 Cursor、File、Socket，应该在使用后及时关闭。若未关闭会导致异常情况下资源对象未被释放的隐患。
    4. 注册对象未反注册
        未反注册会导致观察者列表里维持着对象的引用，阻止垃圾回收。
    5. Handler 临时性内存泄露
        在 Message 中存在对 Handler 的引用，如果 Message 在 MessageQueue 中存在的时间过长，或导致 Handler 无法回收。如果 Handler 是非静态的，则会导致 Activity 或者 Service 无法被回收。AsyncTask 内部也是 Handler 机制，同样存在内存泄漏的风险。
    6. Activity 的长久引用
        对 activity 的引用应该和 activity 本身有相同的生命周期，否则应使用 ApplicationContext。

    可以通过以下工具排查内存泄漏：
    - dumpsys
    通过 `adb shell dumpsys meminfo <packageName>` 可以查看内存使用情况。
    - Memory Monitor
    Android Studio 自带的内存分析器。
    - Android Profiler
    Android Studio 自带分析器。
    - LeakCanary
    在应用中接入 LeakCanary，可以在发生内存泄漏的时候展现可视化报告。
- **OOM（OutOfMemory）\*\*\r\r
为了整个 Android 系统的内存控制需要，Android 系统为每一个应用程序都设置了一个硬性的 Dalvik Heap Size 最大限制阈值，这个阈值在不同的设备上会因为 RAM 大小不同而各有差异。如果你的应用占用内存空间已经接近这个阈值，此时再尝试分配内存的话，很容易引起OutOfMemoryError的错误。
`ActivityManager.getMemoryClass()` 可以用来查询当前应用的 Heap Size 阈值，这个方法会返回一个整数，表明你的应用的 Heap Size 阈值是多少 Mb。
要避免 OOM 的发生，可以从以下角度来优化应用：
    1. 减小对象的内存占用
        - 使用更加轻量的数据结构
        可以考虑使用 ArrayMap/SparseArray 来代替一部分 HashMap。HashMap 因需要一个额外的实例对象来记录 Mapping，在大多数情况下比 ArrayMap 效率更低下、更占内存。而因为 HashMap 会对基础类型进行自动装箱、自动拆箱，SparseArray 相较之下更加高效。
        - 尽量避免使用 Enum
        每一个 Enum 的值都会创建一个对象，并且会急剧增加 DEX 文件的大小。可以考虑使用常量基础类型来替代枚举值，并使用 Annotation（@IntDef、@StringDef 等） 来避免类型不安全的问题。
        - 减小 Bitmap 对象的内存占用
        Bitmap 极容易消耗内存，可以通过 `BitmapFactory.Options.inSampleSize` 设定缩放比例，在把图片载入内存之前，我们需要先计算出一个合适的缩放比例，避免不必要的大图载入，并且可以通过 `Bitmap.Config` 指定解码格式，不同的解码格式占用内存大小存在很大差异。
        - 使用更小的图片
        在使用图片时需要留意这张图片是否存在可压缩的空间，是否可以使用一张更小的图片。假设有一张很大的图片被 xml 文件直接引用，可能会在初始化视图时因内存不足抛出 InflationException，这个问题的本质是抛出了 OOM。
    2. 内存对象的重复利用
        - 复用系统自带的资源
        Android 系统本身内置了很多的资源，例如字符串/颜色/图片/动画/样式以及简单布局等等，这些资源都可以在应用程序中直接引用。这样做不仅仅可以减少应用程序的自身负重，减小 APK 的大小，另外还可以一定程度上减少内存的开销，复用性更好。
        - 列表等控件对子控件的复用
        - Bitmap 对象的复用
        在显示大量图片的场合下使用 LRU 缓存机制。合理运用 `BitmapFactory.Options.inBitmap` 来复用内存空间。
        - 避免在频繁调用的方法内创建对象
        要避免在类似 `onDraw()` 等频繁调用的方法内创建对象，这可能会创建大量临时对象，迅速增加内存的使用，容易引起频繁 GC，甚至是内存抖动。
        - StringBuilder、StringBuffer
        在对字符串进行大量操作的场合下，使用 StringBuilder 或 StringBuffer 来替代原始 String。
    3. 避免对象的内存泄露
        - Activity 的泄露
        Activity 的角色至关重要，一旦 Activity 发生泄露，会占用大量的内存无法释放。需要注意内部类会保持外部对象的引用，在传递 Context 时也需要注意其生命周期，在周期不确定时可以考虑使用 ApplicationContext。
        - 注意临时 Bitmap 对象的及时回收
        在某些情况下 Bitmap 是需要及时回收的。例如临时创建的 Bitmap 对象，在经过变换得到新的 Bitmap 对象之后，应该尽快回收原始 Bitmap。
        需要特别留意的是 `Bitmap.createBitmap()` 方法，这个函数返回的 Bitmap 有可能和 source Bitmap 是同一个，在回收时需要检查引用是否相同。
        - 注销监听器
        监听器在宿主被销毁的时候需要反注册。
        - 注意缓存容器中的对象泄漏
        一些缓存容器中会保持对对象的引用，导致对象没有及时被销毁。
        - WebView 的泄漏
        Android 中的 WebView 存在很大的兼容性问题。通常为 WebView 开启一个独立进程，通过 AIDL 与主进程进行通信，在 WebView 需要销毁时直接销毁进程，使占用的内存完全释放。
        - 一些对象的关闭
        需要留意 Cursor、Stream 等需要手动调用关闭的对象在使用后是否正常关闭。
    4. 内存使用策略优化
        - 谨慎使用 Large Heap
        Android 设备根据设备差异存在不同大小的内存空间，并为应用程序设置了不同大小的 Heap 阈值。可以通过在 `AndroidManifest.xml` 中配置 Application 中的 `android:largeHeap` 属性可以为应用声明更大的 Heap 空间。声明得到更大 Heap 阈值的本意是为了会消耗大量 RAM 的应用 (例如图片编辑应用)。Large Heap 并不一定能够获取到更大的 Heap。在某些有严格限制的机器上，Large Heap 的大小与 Heap Size 相同，因此无论是否申请 Large Heap，都应该通过执行 `getMemoryClass()` 来检查实际获取到的 Heap 大小。
        - 综合考虑设备内存设计缓存大小
        需要考虑各种问题，例如：应用程序剩下了多少可用的内存空间？有多少图片会被同时呈现在屏幕上？有多少图片需要事先缓存好？设备的屏幕大小与密度是多少？不同的页面针对 Bitmap 的设计的尺寸与配置是什么，大概会花费多少内存？页面图片被访问的频率？是否存在其中的一部分比其他的图片具有更高的访问频繁？等等。
        - `onLowMemory()`、`onTrimMemory()`
        Android 提供了一些回调来通知当前应用的内存使用情况，通常来说，当所有的 background 应用都被 kill 掉时，forground 应用会收到 `onLowMemory()` 回调。在这种情况下，需要尽快释放当前应用的非必须的内存资源，从而确保系统能够继续稳定运行。
        Android 系统从 4.0 开始还提供了 `onTrimMemory()` 的回调，收到 `onTrimMemory()` 回调时需要根据参数进行判断，合理选择释放自身的一些内存占用。
        - 资源文件选择合适的文件夹
        若没有合适的图片资源，Android 会从其它 dpi 的文件夹下获取图片并进行 scale 处理。
        - try/catch 某些大内存分配的操作
        可以事先评估那些可能发生 OOM 的代码，对于这些可能发生 OOM 的代码，加入 try/catch 机制，可以考虑在 catch 里面尝试一次降级的内存分配操作。
        - 谨慎使用 static 对象
        static 对象的生命周期过长，和应用的进程保持一致，会常驻于内存中。
        - 留意对象中不合理的持有
        不合理的持有会导致内存泄漏。
        - 珍惜 Services 资源
        当启动一个 Service 时，系统会倾向为了保留这个 Service 而一直保留其所在进程。这使得进程的运行代价很高，因为系统没有办法把 Service 所占用的 RAM 空间腾出来让给其他组件。在 Service 运行结束后要尽快结束自己。
        - 优化布局层次，减少内存消耗
        布局嵌套层次越多，在绘制的时候效率越低。布局越多，占用的内存越多。
        - 谨慎使用 “抽象” 编程
        抽象会导致显著的额外内存开销，抽象方法需要同等量的代码用于执行，那些代码会被 mapping 到内存中，如果抽象没有显著的提升效率，应尽量避免使用。
        - 使用 ProGuard 来剔除不需要的代码
        ProGuard 能够通过移除不需要的代码，重命名类，域与方法等等对代码进行压缩，优化与混淆。
        - 谨慎使用第三方 libraries
        很多开源的 library 代码不一定适合运用在移动设备上。即使是针对 Android 而设计的 library，也需要特别谨慎，特别是在你不知道引入的 library 具体做了什么事情的时候。

    内存优化并不就是说程序占用的内存越少就越好，如果因为想要保持更低的内存占用，而频繁触发执行 GC 操作，在某种程度上反而会导致应用性能整体有所下降，这里需要综合考虑做一定的权衡。

#### 卡顿检测方法
- **StickMode\*\*\r\r
StrictMode 类是 Android 的一个工具类，可以用来帮助开发者发现代码中的一些不规范的问题，以达到提升应用响应能力的目的。可以设置不同的线程检测策略、虚拟机检测策略。当违规操作发生时，可以根据自定义的策略记录下 Log 或者 Crash，以便于跟踪改善。
- **TraceView\*\*\r\r
TraceView 不仅能看出每个方法消耗的时间、发生次数，并且可以进行排序，直接从最耗时的方法开始处优化。
- **AndroidPerformanceMonitor\*\*\r\r
AndroidPerformanceMonitor 是一个检测卡顿的开源库，前身是 BlockCanary，更前身是 LeakCanary。其使用与 LeakCanary 也比较相似，可以自主设置卡顿检测时间，检测到的卡顿以 Notification 展示。原理是利用了 Looper 中每个 Message 被分发前后的 Log。
- **ANR-WatchDog\*\*\r\r
检测卡顿的第三方库。原理是开启一个线程持续循环不断的往主线程中发送 Runnable，然后在规定时间之后检测 Runnable 是否被执行。没有被执行的话说明主线程执行上一个 Message 超时，然后获取当前堆栈信息。
- **Choreographer\*\*\r\r
Android 系统每隔 16ms 都会发出 VSYNC 信号，触发 UI 的绘制，通过监听回调，如果 16ms 没有回调的话就发生了卡顿。这种方式的原理也比较简单，但是可用性不高，只能测出界面绘制的卡顿。

#### ANR
ANR（Application Not Responding）指程序未响应。Android 系统中，AMS（ActivityManagerService）和 WMS（WindowManagerService）会检测 App 的响应时间，如果 App 在特定时间无法相应屏幕触摸或键盘输入时间，或者特定事件没有处理完毕，就会出现 ANR。
ANR 的原理是在执行方法前发送一个延时的 ANR 任务，并在方法执行完时取消延时任务。若是方法未执行完，会执行延时任务触发 ANR。
以下四个条件都可以造成 ANR 发生：
- **InputDispatching Timeout\*\*\r\r
5 秒内无法响应屏幕触摸事件或键盘输入事件。
- **BroadcastQueue Timeout\*\*\r\r
BroadcastReceiver 的 `onReceive()` 在 10 秒没有处理完成。
- **Service Timeout\*\*\r\r
ForegroundService 需要在 5 秒内执行 `startForeground()`，后台 Service 在 200 秒内没有完成任务。
- **ContentProvider Timeout\*\*\r\r
ContentProvider 的 publish 在 10 秒内没进行完。

ANR 的 Log 信息保存在 `/data/anr/traces.txt` 中，每一次新的 ANR 发生，会把之前的 ANR 信息覆盖掉。从日志中可以得到信息：导致 ANR 的包名、类名、进程 PID、导致 ANR 的原因、系统中活跃进程的 CPU 占用率等。

#### 网络优化
过多、过慢的网络请求会导致用户消耗流量、消耗电量、等待时间过长，还会降低版本更新的比例、热更新的送达率、成功率等等。
可以从以下几个方面进行优化：
1. Gzip
    HTTP 协议上的 Gzip 编码是一种用来改进 WEB 应用程序性能的技术，用来减少传输数据量大小，可以减少流量消耗和传输时间。
2. IP 直连
    DNS 解析失败占联网失败中很大一部分，域名首次解析一般需要几百毫秒。针对此，我们可以不用域名，用 IP 直连省去 DNS 解析过程，节省这部分时间。
3. HttpDNS
    HttpDNS 基于 Http 协议的域名解析，替代了基于 DNS 协议向运营商 Local DNS 发起解析请求的传统方式，可以避免 Local DNS 造成的域名劫持和跨网访问问题，解决域名解析异常带来的困扰。
4. 图片下载
    网络端图片可以采用 WebP 格式，同样大小、质量的图片，比 JPG 缩小约 30%，比 PNG 缩小约 80%。在请求图片时按需下载，根据需求的尺寸下载合适的图片。
5. 文件处理
    文件下载、上传的失败率较高，尽量避免整个文件传输，采用分片传输、断点传输方式。
6. 请求打包
    合并部分网络请求，减少请求次数。例如一些统计、日志等信息，可以先保存在本地，根据策略统一上传。
7. 网络缓存
    根据接口的需求和内容，设定合适的缓存机制。
8. 网络状态
    根据网络状态对网络请求进行区别对待。例如：在 Wi-Fi 场景下可以进行数据的预取、一些统计的集中上传等；而在 2G 场景下此类操作以及网络请求的次数策略都应该调低。
9. 增量数据
    根据实际情况采用接口返回增量数据，由客户端合并的策略，减少传输的数据量。
可以使用以下工具检测网络：
- Network Monitor
Android Studio 自带的网络分析器。可以查看网络请求的数量、频率、分布和速度。
- Charles、Fiddler 等抓包工具
- Stetho
Stetho 是 Facebook 出品的一个 Android 应用的调试工具。无需 Root 即可通过 Chrome，在 Chrome Developer Tools 中可视化查看应用布局、网络请求、SQLite、Preference 等。

#### APK 瘦身
APK 文件过大会影响用户下载的倾向。可以从以下几个方面进行优化：
1. 移除无用代码
    移除不再使用的代码，能减少 DEX 文件体积。
2. 移除多余的依赖库
    移除不再使用的依赖库，功能重叠的库选择一个即可，选择第三方库时权衡功能与体积。
3. 启用 ProGuard
    ProGuard 可以移除一部分冗余代码。
4. 缩减方法数
    过多的方法数会影响 DEX 文件体积。
5. 移除无用的资源文件
    移除不再使用的资源文件，能显著减小安装包体积。
6. Drawable 资源分配
    多个 Drawable 文件夹只有一个被用户最终使用，其它的都是冗余的。
7. 采用有损图片格式
    在肉眼基本分辨不出的前提下，有损图片体积显著减少。即使相同格式的图片，经过压缩后体积也显著减少。
8. 使用 WebP
    WebP 文件在图片质量一样的前提下体积更小。
9. 使用矢量图
    矢量图一个图片即可适配所有机型。但是会减低渲染效率。
10. 资源混淆
    resources.arsc 文件变小，文件信息变小。
11. 资源在线化
    将一部分使用频率低的资源放在服务器，动态按需获取。
12. 选择特定架构的 SO 文件
    多个 SO 文件中只有一个被用户最终使用，其它的都是冗余的。armeabi 架构的 SO 文件兼容其他架构，但会损失一定效率。
13. 二次压缩 APK 文件
    可以使用压缩工具对 APK 解压出的文件重新压缩，选择最高的压缩率。注意压缩仅支持 Deflate 算法。
14. 插件化
    一部分使用频率低下的模块可以使用插件化动态按需加载。
*Google Play 新增（2018/09/20）了动态打包功能，在 Google Play 上上传的 APK 文件会根据用户的设备，只打包适合的 Drawable 文件夹和 SO 文件。*
可以使用以下工具分析 APK 文件：
- Analyze APK
Android Studio 自带的 APK 分析工具。可以对整个 APK 的结构和空间占用比例有直观的了解。
- Nimbledroid
NimbleDroid 是美国哥伦比亚大学的博士创业团队研发出来的分析 Android App 性能指标的系统。

[Home](../../README.md)