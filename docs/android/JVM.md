[Home](../../README.md)

# Android

## JVM

#### Dalvik
Dalvik 是最早 Google 针对 Android 开发的虚拟机。
Dalvik 有以下特点：
- **JIT（Just In Time）**
在 android2.2 以上版本，Dalvik 如同其他大多数 JVM 一样采用了 JIT 来做及时翻译 (动态翻译)，将 DEX 或 ODEX 中并排的 Dalvik Code（即 smali 指令集）运行态翻译成 native code 去执行。
JIT 的引入使得 Dalvik 提升了 3~6 倍的性能。
- **GC 机制**
Dalvik 在触发 GC 机制时，会挂起整个程序与虚拟机内部的所有线程来查找活动对象，这样目的是在较少的堆栈里找到所引用的对象。这个回收动作是和应用程序同时执行（非并发）。这么做的好处是，在挂起整个程序后 GC 势必是相当快速的。但如果频繁出现 GC 并且内存吃紧时，会导致卡顿、掉帧、操作不流畅等。
- **标记-清除（Mark and Sweep）**
Dalvik 在 GC 时，标记不需要保留的对象并清除，但是没有做内存整理相关的工作，导致内存碎片化严重，创建对象时寻址困难。

#### ART（Android Runtime）
ART 是 Android 4.4 新增的虚拟机，并在 Android 5.0 以上全面替代 Dalvik。
ART 有以下特点：
- **AOT（Ahead Of Time）**
ART 在程序执行之前（一般是安装时）就编译好本地代码，因此程序执行时少了编译过程，会更快一些，但占用更多存储空间，安装时也会更慢。
- **GC 机制**
ART 拥有改进的 GC 机制，GC 时更少地暂停时间、GC 时并行处理、减少内存不足时触发 GC 的次数、减少后台内存占用。
- **压缩内存**
ART 在 GC 时，清除不需要保留的对象后，MovingCollector 会压缩内存，使内存更紧凑。
- **更多调试功能**
ART 支持了更多的调试功能，例如：
    - 查看堆栈跟踪中保留了哪些锁，然后跳转到持有锁的线程。
    - 询问指定类的当前活动的实例数、请求查看实例，以及查看使对象保持有效状态的参考。
    - 过滤特定实例的事件（如断点）。
    - 查看方法退出（使用 “method-exit” 事件）时返回的值。
    - 设置字段观察点，以在访问和/或修改特定字段时暂停程序执行。
- **Android 7.0 的改进**
Android 7.0 的 ART 引入了全新的 Hybrid 模式（Interpreter + JIT + AOT）。App 在安装时不编译，安装速度快。在运行 App 时，先走解释器，然后热点函数会被识别并被 JIT 进行编译，存储在 JIT Code Cache 中，并产生 profile 文件记录热点函数信息。等手机进入 Charging 和 Idle 状态时，系统会每隔一段时间扫描 App 目录下 profile 文件并执行 AOT 编译（Google 官方称之为 profile-guided compilation）。App 安装速度快，占用存储少，但是前几次运行会较慢，只有用户操作的次数多了，运行速度才会跟上来。

[Home](../../README.md)