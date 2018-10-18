[Home](../../README.md)  

# Android  

## Framework  
![image](https://user-images.githubusercontent.com/8423120/46943826-1d065100-d0a3-11e8-8159-0ec2c9d3070c.png)  

#### Init  
Init 是一个进程，确切地说，它是 Linux 系统中用户空间的第一个进程。由于 Android 是基于 Linux 内核的，所以 Init 也是 Android 系统中用户空间的第一个进程，它的进程号是 1。Init 被赋予了很多极其重要的工作职责，其中两个比较重要的职责：  
1. Init 进程负责创建系统中的几个关键进程，其中包括 Zygote。  
2. Android 系统有很多属性，于是 Init 就提供了一个 Property Service（属性服务）来管理它们。  

#### Zygote  
Zygote 是一个孕育器，它创建了 JVM 虚拟机，并且 Android 系统所有的应用进程以及系统服务 SystemServer 都是由 Zygote 进程孕育（fork）而生的。  
`ZygoteInit.main()` 是整个 Java 的入口，主要执行了以下操作：  
1. `registerZygoteSocket()`  
开启 LocalServerSocket，其中传入 FileDescriptor，并根据 FileDescriptor 创建一个 LocalServerSocket（LocalSocketImpl）并处于 listen 状态。  
- `preload()`  
预加载一些资源，其中包括：  
    1. 预加载 ICU 缓存；  
    2. 执行 Zygote 进程初始化，预加载一些普通类；  
    3. 预加载 mResources；  
    4. 预加载 OpenGL；  
    5. 预加载共享库；  
    6. 预加载 TextView 的字体缓存。  
2. `gcAndFinalize()`  
释放一些内存。  
3. `startSystemServer()`  
启动 SystemServer 组件，其中包括：  
    1. `Zygote.forkSystemServer()` 方法 fork 出一个新进程并返回 pid；  
    2. 如果 pid 为 0 且存在另一个 zygote 进程则会执行 `waitForSecondaryZygote()` 关闭另外的 zygote 进程；  
    3. `handleSystemServerProcess()`  
        1. 调用 `closeServerSocket()` 方法关闭 socket；  
        2. 调用 `performSystemServerDexOpt()` 来创建安装连接 InstallerConnection；  
        3. 调用 `RuntimeInit.zygoteInit()` 方法。  
            1. 调用 `nativeZygoteInit()` 函数来执行一个 Binder 进程间通讯机制初始化工作，然后就可以在进程间进行通讯了；  
            2. 执行 `applicationInit()` 方法。其内部主要是执行 `invokeStaticMain()` 方法，最终调用 `SystemServer.main()`。  
4. `runSelectLoop()`  
创建一个无限循环，在 socket 接口上等待 ActivityManagerService 请求创建新的应用程序进程。  
    1. 通过 `acceptCommandPeer()` 来创建 ActivityManagerService 与 Socket 的连接；  
    2. 调用 `ZygoteConnection.runOnce()` 方法来创建新的应用程序。  
5. `closeServerSocket()`  
关闭上面创建的 socket。  

![image](https://user-images.githubusercontent.com/8423120/46988047-5e414400-d129-11e8-94b5-3a9bbbde88ff.png)  

#### SystemServer  
SystemServer 正如其名，和系统服务有着重要关系。如果 SystemServer 进程死亡，Zygote 进程会自杀，最终 Init 进程会重启 Zygote。Android 系统中几乎所有的核心 Service 都在这个进程中，具体可以分为以下几类：  
- 位于第一大类的是 Android 的核心服务，如 ActivityManagerService、WindowManagerService 等。  
- 位于第二大类的是和通信相关的服务，如 Wifi 相关服务、Telephone 相关服务。  
- 位于第三大类的是和系统功能相关的服务，如 AudioService、MountService、UsbService 等。  
- 位于第四大类的是 BatteryService、VibratorService 等服务。  
- 位于第五大类的是 EntropyService，DiskStatsService、Watchdog 等相对独立的服务。  
- 位于第六大类的是蓝牙服务  
- 位于第七大类的是 UI 方面的服务，如状态栏服务，通知管理服务等。  
![image](https://user-images.githubusercontent.com/8423120/46723755-2494b700-ccab-11e8-9c05-5cf52548b75a.png)  
`SystemServer.main()` 中调用了 `SystemServer.run()` 方法，其具体启动过程：  
1. 准备工作  
    做一些初始化前的准备工作，主要是虚拟机内存、进程优先级等。  
2. `Looper.prepareMainLooper()`  
3. `SystemServer.performPendingShutdown()`  
    判断是否是重启或者关机。  
4. `SystemServer.createSystemContext()`  
    1. 调用 `ActivityThread.systemMain()` 方法创建 ActivityThread 对象，并调用 `ActivityThread.attach()` 创建一个 SystemContext；  
    2. 获取 mSystemContext；  
    3. 设置默认系统主题。  
5. `SystemServer.startBootstrapServices()`  
    启动系统引导服务。  
6. `SystemServer.startCoreServices()`  
    启动核心服务，例如电池服务、应用统计服务等。  
7. `SystemServer.startOtherServices()`  
    启动其它的系统服务。  

![image](https://user-images.githubusercontent.com/8423120/46988138-d0b22400-d129-11e8-9569-8b634b57f7d8.png)  

#### Context  
![image](https://user-images.githubusercontent.com/8423120/46944375-702cd380-d0a4-11e8-9744-733cfb37e3b2.png)  
Context 是一个抽象类，它的实现是 ContextImpl，并有一个包装类 ContextWrapper，其中 ContextWrapper 保持了 Context 的引用，并包装了 Context 的所有方法，ContextWrapper 中 Context 的具体实现类是 ContextImpl。  
Application、Activity、Service 均调用了 `ContextWrapper.attachBaseContext()` 方法。其中 Application、Service 继承自 ContextWrapper，Activity 继承自 ContextThemeWrapper，ContextThemeWrapper 继承了 ContextWrapper。  
ApplicationContext 的来源：  
`Application.attatchBaseContext()` → `Application.attach()` → `Instrumentation.newApplication()` → `LoadApk.makeApplication()` → `ContextImpl.createAppContext()`  
Service 的来源：  
`Service.attatchBaseContext()` → `Service.attach()` → `ActivityThread.handleCreateService()` → `ContextImpl.createAppContext()`  
Activity 的来源：  
`Activity.attachBaseContext()` → `Activity.attach()` → `ActivityThread.performLaunchActivity()` → `ActivityThread.createBaseContextForActivity()` → `ContextImpl.createActivityContext()`  

[Home](../../README.md)  