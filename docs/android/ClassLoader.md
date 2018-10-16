[Home](../../README.md)

# Android

## ClassLoader
Android 加载的不再是 Class 文件，而是 DEX 文件，这就需要重新设计 ClassLoader 相关类。
Android 中的 ClassLoader 分为两种类型：系统 ClassLoader 和自定义 ClassLoader。其中系统 ClassLoader 包括三种：BootClassLoader、PathClassLoader 和 DexClassLoader。

#### ODEX
当 Android 系统安装一个应用的时候，会针对不同平台对 DEX 进行优化，这个过程由一个专门的工具 DexOpt 来处理。DexOpt 是在第一次加载 DEX 文件的时候执行的，该过程会生成一个 ODEX 文件，即 Optimised DEX。执行 ODEX 的效率会比直接执行 DEX 文件的效率要高很多，加快 App 的启动和响应。

#### Android 提供的 ClassLoader
- **BootClassLoader**
Android 系统启动时会创建 BootClassLoader 用于预加载常用类，与 Java 中的 BootClassLoader 不同，它并不是由 C/C++ 代码实现，而是由 Java 实现的。
- **PathClassLoader**
PathClassLoader 在应用启动时创建，用于加载系统类和应用程序的类。PathClassLoader 只能加载已经安装应用的 DEX 或 APK 文件。
- **DexClassLoader**
DexClassLoader 可以加载 DEX 文件以及包含 DEX 的 APK 文件或 JAR 文件。DexClassLoader 可以在应用未安装的情况下加载 DEX 相关文件。它是热修复和插件化技术的基础。
- **BaseClassLoader**
PathClassLoader、DexClassLoader 都继承自 BaseClassLoader，且大部分实现都在 BaseClassLoader 中。
- **SecureClassLoader**
与 JDK8 中的 SecureClassLoader 类的代码一样，它继承了抽象类 ClassLoader。SecureClassLoader 并不是 ClassLoader 的实现类，而是拓展了 ClassLoader 类加入了权限方面的功能，加强了 ClassLoader 的安全性。
- **URLClassLoader**
与 JDK8 中的 URLClassLoader 类的代码一样，它继承自 SecureClassLoader，用来通过 URL 路径从 JAR 文件和文件夹中加载类和资源。
- **InMemoryDexClassLoader**
Android8.0 新增的类加载器，继承自 BaseDexClassLoader，用于加载内存中的 dex 文件。

![image](https://user-images.githubusercontent.com/8423120/46193140-b6a5d280-c32f-11e8-9d85-618b14afb3eb.png)

[Home](../../README.md)