[Home](../../README.md)  

# Android  

## Plugin & Module  
模块化就是基于可重用的目的，将一个大的软件系统按照分离关注点的形式，拆分成多个独立的模块，以较少耦合、提升长远收益。  
插件化指应用可以按需下载单独的功能模块，并动态加载该功能模块。  
传统意义上的组件化和插件化是两条路：组件化发生在开发阶段、插件化发生在运行阶段。  

#### 热更新  
使用户不用手动下载安装包即可修复/更新线上版本的问题。  
几大主流技术方案：  
- Dynamic-Load-Apk  
ClassLoader + ProxyActivity + Reflection  
- QZONE && Nuwa  
ClassLoader + Reflection + Hack.Dex & ASM 注入  
- Tinker  
多 ClassLoader + Reflection + 合并 DEX  
- Instant Run  
ClassLoader + Reflection  
- Robust  
ClassLoader + Reflection  
- AndFix  
ArtMethod，底层替换方案（inline hook）  
- Sophix  
以类为单位，同时使用 ArtMethod 和 ClassLoader。  
- DroidPlugin  
最多的系统 hook + 多 ClassLoader + Reflection  
- RePlugin  
最少的系统 hook + ClassLoader + Reflection  
- VirtualApk  
少量的 hook + ClassLoader + Reflection  
- Small  
ClassLoader + 修改 resources.arsc 重设资源  

#### 组件化  
组件化的目的为了各个业务组件可以单独运行。组件之间的通讯、跳转必然会遇到问题，所以组件化的一大前提是支持隐示跳转的路由。  
在开发中可以将 Module 分为两个模式：Release 模式和 Debug 模式，Release 模式下作为 Library，供宿主依赖；而在 Debug 模式下则作为 Application 工程，自己单独运行。  
Host 可以依赖 Module 提供的 AAR 来避免编译时错误。  
Module 在 Debug 下可以有自己的 Application 类进行一些初始化操作。  
Module 中的资源文件需要添加统一前缀以防资源冲突。  

#### 插件化  
插件化一般有三种技术方案：  
- **动态替换**  
也就是 Hook。可以在不同层次进行 Hook，从而动态替换也细分为若干小流派。可以直接在 Activity 里做 Hook，重写 getAsset 的几个方法，从而使用自己的 ResourceManager 和 AssetPath；也可以在更抽象的层面，也就是在 startActivity 方法的位置做 Hook，涉及的类包括 ActivityThread、Instrumentation 等；最高层次则是在 AMS 上做修改，也就是张勇的解决方案，这里需要修改的类非常多，AMS、PMS 等都需要改动。总之，在越抽象的层次上做 Hook，需要做的改动就越大，但是也更加灵活。  
- **静态代理**  
写一个 PluginActivity 继承自 Activity 基类，把 Activity 基类里面涉及生命周期的方法全都重写一遍，插件中的 Activity 是没有生命周期的，所以要让插件中的 Activity 都继承自 PluginActivity，这样就有生命周期了。  
- **DEX 合并**  
这是 Android 热修复的思想。原生 APK 自带的 DEX 是通过 PathClassLoader 来加载的，而插件 DEX 则是通过 DexClassLoader 来加载的。但有一个顺序问题，如果宿主 DEX 和插件 DEX 都有一个相同命名空间的类的方法，那么先加载哪个 Dex，就会执行哪个 Dex 中的这个类的方法。  

[Home](../../README.md)  