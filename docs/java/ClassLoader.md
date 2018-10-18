[Home](../../README.md)  

# Java  

## ClassLoader  
任何一个 Java 程序都是由若干个 class 文件组成的一个完整的 Java 程序，在程序运行时，需要将 class 文件加载到 JVM 中才可以使用，负责加载这些 class 文件的就是 Java 的类加载（ClassLoader）机制。  

#### Java 提供的 ClassLoader  
- **BootStrap ClassLoader**  
称为启动类加载器，是 Java 类加载层次中最顶层的类加载器，负责加载 JDK 中的核心类库，如：rt.jar、resources.jar、charsets.jar 等。Bootstrap ClassLoader 不继承自 ClassLoader，它不是一个普通的 Java 类，底层由 C++ 编写，已嵌入到了 JVM 内核中，当 JVM 启动时 Bootstrap ClassLoader 也随之启动，负责加载完核心类库后，并构造 Extension ClassLoader 和 App ClassLoader 类加载器。  
- **Extension ClassLoader**  
扩展类加载器，负责加载 Java 的扩展类库，默认加载 JAVA_HOME/jre/lib/ext / 目下的所有 jar。继承自 ClassLoader。  
- **App ClassLoader**  
系统类加载器，负责加载应用程序 classpath 目录下的所有 jar 和 class 文件。继承自 ClassLoader。  

#### 双亲委托模型（Parent Delegation Model）  
ClassLoader 使用双亲委托模型来搜索类，每个 ClassLoader 实例都有一个父类加载器的引用，虚拟机内置的类加载器（Bootstrap ClassLoader）本身没有父类加载器，但可以用作其它 ClassLoader 实例的的父类加载器。当一个 ClassLoader 实例需要加载某个类时，它会先把这个任务委托给它的父类加载器，整个过程由上至下，先由最顶层的类加载器 Bootstrap ClassLoader 试图加载，若没加载到，则把任务转交给 Extension ClassLoader 试图加载，如果也没加载到，则转交给 App ClassLoader 进行加载，如果它也没有加载得到的话，则返回给委托的发起者，由它到指定的文件系统或网络等 URL 中加载该类。如果它们都没有加载到这个类时，则抛出 ClassNotFoundException。否则将这个找到的类生成一个类的定义，并将它加载到内存当中，最后返回这个类在内存中的 Class 实例对象。  
![image](https://user-images.githubusercontent.com/8423120/46191908-d20ede80-c32b-11e8-8686-456cd17289f5.png)  
这样设计可以避免重复加载，并且相对更安全。  

#### 如何判断两个 class 是否相同  
JVM 在判定两个 class 是否相同时，不仅要判断两个类名是否相同，而且要判断是否由同一个类加载器实例加载的。只有两者同时满足的情况下，JVM 才认为这两个 class 是相同的。就算两个 class 是同一份 class 字节码，如果被两个不同的 ClassLoader 实例所加载，JVM 也会认为它们是两个不同 class。  

#### 自定义 ClassLoader  
除了 Java 默认提供的三个 ClassLoader 之外，还可以根据需要自定义 ClassLoader，这些自定义 ClassLoader 都必须继承自 java.lang.ClassLoader 类。定义自已的类加载器分为两步：继承 java.lang.ClassLoader、重写父类的 `findClass()` 方法。  
JDK 已经在 `loadClass()` 方法中已经实现了 ClassLoader 搜索类的算法，当在 `loadClass()` 方法中搜索不到类时，`loadClass()` 方法会调用 `findClass()` 方法来搜索类，所以只需重写该方法即可。如没有特殊的要求，一般不建议重写 `loadClass()` 搜索类的算法。  


[Home](../../README.md)  