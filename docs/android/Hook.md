[Home](../../README.md)

# Android

## Hook

#### Xposed Installer
在 Android 系统中 App 进程都是由 Zygote 进程“孵化”而来。Zygote 进程在启动时会创建一个虚拟机实例，每当它“孵化”一个新的应用程序进程时，都会将 JMV 复制到新的 App 进程里面去，从而使每个 App 进程都有一个独立的 JVM。
Zygote 进程在启动的过程中，除了会创建一个 JVM 实例之外还会将 Java Rumtime 加载到进程中并注册一些 Android 核心类的 JNI（Java Native Interface）方法。一个 App 进程被 Zygote 进程孵化出来时，不仅会获得 Zygote 进程中的 JVM 实例拷贝，还会与 Zygote 进程一起共享 Java Rumtime，也就是可以将 XposedBridge.jar 这个 Jar 包加载到每一个 Android App 进程中去。安装 Xposed Installer 之后，系统 app_process 将被替换，然后利用 Java 的 Reflection 机制覆写内置方法，实现功能劫持。
Xposed Installer 框架中真正起作用的是对方法的 Hook 和 Replace。在 Android 系统启动的时候，Zygote 进程加载 XposedBridge.jar，将所有需要替换的 Method 通过 JNI 方法 hookMethodNative 指向 Native 方法 xposedCallHandler，这个方法再通过调用 handleHookedMethod 这个 Java 方法来调用被劫持的方法转入 Hook 逻辑。
![](https://user-images.githubusercontent.com/8423120/46198997-64b97880-c340-11e8-8b8b-fa3df3164c24.png)

#### 检测 Xposed Installer
在做 Android App 的安全防御中检测点众多，Xposed Installer 检测是必不可少的一环。对于 Xposed 框架的防御总体上分为两层：Java 层和 Native 层。
- **Java 层检测**
Java 层的检测基本只能检测出基础的 Xposed Installer 框架，而不能防护其对 App 内方法的 Hook，如果框架中带有反检测则 Java 层检测大多不起作用。可以通过以下方法尝试检测：
1. 通过 PackageManager 查看安装列表
最简单的检测，我们调用 Android 提供的 PackageManager 的 API 来遍历系统中 App 的安装情况来辨别是否有安装 Xposed Installer 相关的软件包。
```java
PackageManager packageManager = context.getPackageManager();
List applicationInfoList = packageManager.getInstalledApplications(PackageManager.GET_META_DATA);
for (ApplicationInfo applicationInfo: applicationInfoList) {
    if (applicationInfo.packageName.equals("de.robv.android.xposed.installer")) {
        //TODO contains Xposed... }
    }
```
通常情况下使用 Xposed Installer 框架都会屏蔽对其的检测，即 Hook 掉 PackageManager 的 getInstalledApplications 方法的返回值，以便过滤掉 de.robv.android.xposed.installer 来躲避这种检测。
2. 自造异常读取栈
Xposed Installer 框架对每个由 Zygote 孵化的 App 进程都会介入，因此在程序方法异常栈中就会出现 Xposed 相关的 “身影”，我们可以通过自造异常 Catch 来读取异常堆栈的形式，用以检查其中是否存在 Xposed 的调用方法。
```java
try {
    throw new Exception("blah");
} catch(Exception e) {
    for (StackTraceElement stackTraceElement: e.getStackTrace()) {
        // stackTraceElement.getClassName() OR stackTraceElement.getMethodName() 是否存在 Xposed
    }
}
```
3. 检查关键 Java 方法被变为 Native JNI 方法
当一个 Android App 中的 Java 方法变成了 Native JNI 方法，非常有可能已经被 Xposed Hook 了。由此可得，检查关键方法是不是变成 Native JNI 方法，也可以检测是否被 Hook。
通过反射调用 `Modifier.isNative(method.getModifiers())` 方法可以校验方法是不是 Native JNI 方法，但是 Xposed 同样可以篡改 `Modifier.isNative` 这个方法的返回值。
4. 反射读取 XposedHelper 类字段
通过反射遍历 XposedHelper 类中的 fieldCache、methodCache、constructorCache 变量，读取 HashMap 缓存字段，如字段项的 key 中包含 App 中唯一或敏感方法等，即可认为有 Xposed 注入。
```java
private static boolean CheckHook(Object cls, String filedName, String str) {
    boolean result = false;
    String interName;
    Set keySet;
    try {
        Field filed = cls.getClass().getDeclaredField(filedName);
        filed.setAccessible(true);
        keySet = filed.get(cls)).keySet();
        if (!keySet.isEmpty()) {
            for (Object aKeySet: keySet) {
                interName = aKeySet.toString().toLowerCase();
                if (interName.contains("meituan") || interName.contains("dianping") ) {
                    result = true;
                    break;
                    }
                }
            }
        ...
    return result;
}
```
- **Native 层检测**
无论在 Java 层做何种检测，Xposed 都可以通过 Hook 相关的 API 并返回指定的结果来绕过检测，只要有方法就可以被 Hook，为了有效提搞检测准确率，就须做到 Java 和 Native 层同时检测。每个 App 在系统中都有对应的加载库列表，这些加载库列表在 /proc/ 下对应的 pid/maps 文件中描述，在 Native 层读取 /proc/self/maps 文件不失为检测 Xposed Installer 的有效办法之一。由于 Xposed Installer 通常只能 Hook Java 层，因此在 Native 层使用 C 来解析 /proc/self/maps 文件，搜检 App 自身加载的库中是否存在 XposedBridge.jar、相关的 DEX、JAR 和 SO 库等文件。

#### Cydia Substrate
Cydia Substrate 是一个代码修改平台。它可以修改任何主进程的代码，不管是用 Java 还是 C/C++（native 代码）编写的。

#### 检测 Cydia Substrate
Cydia Substrate 的检测主要在 Native 层来实现。
- **动态加载式检测**
读取 /proc/self/maps，列出了 App 中所有加载的文件。其中 libsubstrate.so 和 libsubstrate-dvm.so 两个文件为 Substrate 必载入文件。当进程 maps 表中出现 libsubstrate-dvm.so，可以尝试去 load 该 SO 文件并调用 MSJavaHookMethod 方法，它会返回该方法的地址即判定为恶意模块（第三方程序）。
```c++
void* lookup_symbol(char* libraryname,char* symbolname)
{
    void *imagehandle = dlopen(libraryname, RTLD_GLOBAL | RTLD_NOW);
    if (imagehandle != NULL){
        void * sym = dlsym(imagehandle, symbolname);
        if (sym != NULL){
            return sym; //发现Cydia Substrate相关模块
            }
      ...
}
```
- **基于方法特征码检测**
特征码即用来判断某段数据属于哪个计算机字段。在非 Root 环境下一般一个正常 App 在启动时候，系统会调度相关大小的内存、空间给 App 使用，此时 App 的运行环境内产生的数据、内存、存储等是独立于其它 App 的（即独立运行在沙箱中）。因为处于运行沙箱环境中的进程对沙箱的内存有最高读写权限，当我们的 App 进程被恶意模块附加或注入时，就可以通过对当前进程的 PID 所对应的 maps 中加载的模块进行合法校验。这里的模块校验我们可以采取对单个模块内容取样来判断是否为恶意模块，这种方式被定义为“基于方法的特征码检测”。

[Home](../../README.md)