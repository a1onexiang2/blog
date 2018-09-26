[Home](../../README.md)

# Android

## Memory

#### 共享内存
Android 系统通过下面几种方式来实现共享内存：
Android 应用的进程都是从 Zygote 的进程 fork 出来的。Zygote 进程在系统启动并且载入通用的 framework 的代码与资源之后开始启动。为了启动一个新的程序进程，系统会 fork Zygote 进程生成一个新的进程，然后在新的进程中加载并运行应用程序的代码。这使得大多数的 RAM pages 被用来分配给 framework 的代码，同时使得 RAM 资源能够在应用的所有进程之间进行共享。
大多数 static 的数据被 mmapped 到一个进程中。这不仅仅使得同样的数据能够在进程间进行共享，而且使得它能够在需要的时候被 paged out。常见的 static 数据包括 Dalvik Code，app resources，so 文件等。
大多数情况下，Android 通过显式的分配共享内存区域 (例如 ashmem 或者 gralloc) 来实现动态 RAM 区域能够在不同进程之间进行共享的机制。例如，Window Surface 在 App 与 Screen Compositor 之间使用共享的内存，Cursor Buffers 在 ContentProvider 与 Clients 之间共享内存。

#### Memory Leak

#### OOM（OutOfMemory）
为了整个 Android 系统的内存控制需要，Android 系统为每一个应用程序都设置了一个硬性的 Dalvik Heap Size 最大限制阈值，这个阈值在不同的设备上会因为 RAM 大小不同而各有差异。如果你的应用占用内存空间已经接近这个阈值，此时再尝试分配内存的话，很容易引起OutOfMemoryError的错误。
`ActivityManager.getMemoryClass()` 可以用来查询当前应用的 Heap Size 阈值，这个方法会返回一个整数，表明你的应用的 Heap Size 阈值是多少 Mb。
要避免 OOM 的发生，可以从以下角度来优化应用：
- **减小对象的内存占用**
    - 使用更加轻量的数据结构
    可以考虑使用 ArrayMap/SparseArray 来代替一部分 HashMap。HashMap 因需要一个额外的实例对象来记录 Mapping，在大多数情况下比 ArrayMap 效率更低下、更占内存。而因为 HashMap 会对基础类型进行自动装箱、自动拆箱，SparseArray 相较之下更加高效。
    - 尽量避免使用 Enum
    每一个 Enum 的值都会创建一个对象，并且会急剧增加 DEX 文件的大小。可以考虑使用常量基础类型来替代枚举值，并使用 Annotation（@IntDef、@StringDef 等） 来避免类型不安全的问题。
    - 减小 Bitmap 对象的内存占用
    Bitmap 极容易消耗内存，可以通过 `BitmapFactory.Options.inSampleSize` 设定缩放比例，在把图片载入内存之前，我们需要先计算出一个合适的缩放比例，避免不必要的大图载入，并且可以通过 `Bitmap.Config` 指定解码格式，不同的解码格式占用内存大小存在很大差异。
    - 使用更小的图片
    在使用图片时需要留意这张图片是否存在可压缩的空间，是否可以使用一张更小的图片。假设有一张很大的图片被 xml 文件直接引用，可能会在初始化视图时因内存不足抛出 InflationException，这个问题的本质是抛出了 OOM。
- **内存对象的重复利用**
    - 复用系统自带的资源
    Android 系统本身内置了很多的资源，例如字符串/颜色/图片/动画/样式以及简单布局等等，这些资源都可以在应用程序中直接引用。这样做不仅仅可以减少应用程序的自身负重，减小 APK 的大小，另外还可以一定程度上减少内存的开销，复用性更好。
    - 列表等控件对子控件的复用
    - Bitmap 对象的复用
    在显示大量图片的场合下使用 LRU 缓存机制。合理运用 `BitmapFactory.Options.inBitmap` 来复用内存空间。
    - 避免在频繁调用的方法内创建对象
    要避免在类似 `onDraw()` 等频繁调用的方法内创建对象，这可能会创建大量临时对象，迅速增加内存的使用，容易引起频繁 GC，甚至是内存抖动。
    - StringBuilder、StringBuffer
    在对字符串进行大量操作的场合下，使用 StringBuilder 或 StringBuffer 来替代原始 String。
- **避免对象的内存泄露**
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
- **内存使用策略优化**
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
    - 谨慎使用“抽象”编程
    抽象会导致显著的额外内存开销，抽象方法需要同等量的代码用于执行，那些代码会被 mapping 到内存中，如果抽象没有显著的提升效率，应尽量避免使用。
    - 使用 ProGuard 来剔除不需要的代码
    ProGuard 能够通过移除不需要的代码，重命名类，域与方法等等对代码进行压缩，优化与混淆。
    - 谨慎使用第三方 libraries
    很多开源的 library 代码不一定适合运用在移动设备上。即使是针对 Android 而设计的 library，也需要特别谨慎，特别是在你不知道引入的 library 具体做了什么事情的时候。

内存优化并不就是说程序占用的内存越少就越好，如果因为想要保持更低的内存占用，而频繁触发执行 GC 操作，在某种程度上反而会导致应用性能整体有所下降，这里需要综合考虑做一定的权衡。

[Home](../../README.md)