[Home](../../README.md)

# Android

## BroadcastReceiver
进程对象 ProcessRecord 中有一个广播的列表 ArraySet，这个列表表示该进程注册的所有广播接收者，每个 ReceiverList 对象包含了一个广播接收者（实现了 IIntentReceiver 接口的 Binder 对象）封装和与该广播接收者对应的多个 Action 对应的 IntentFilter 对象的封装 BroadcastFilter 列表，这个 ReceiverList 对象将注册的广播接收者以及对应的多个 Action 对应起来，这样就能查找对应的广播接收者。

#### BroadcastReceiver 注册流程
![image](https://user-images.githubusercontent.com/8423120/46995053-9ce6f680-d149-11e8-8132-830f58355775.png)

#### BroadcastReceiver 注销流程
![image](https://user-images.githubusercontent.com/8423120/46995080-bab45b80-d149-11e8-968a-4c123cfc092f.png)

#### BroadcastReceiver 发送流程
![image](https://user-images.githubusercontent.com/8423120/46995997-9b1f3200-d14d-11e8-9fa4-31fe49aa2b93.png)

#### BroadcastReceiver 处理流程
![image](https://user-images.githubusercontent.com/8423120/46998685-de7d9e80-d155-11e8-8d61-315cd045ca9c.png)

#### 广播有几种形式？什么特点？
- **普通广播**
Android 3.1 以上版本可以 通过 `Intent.addFlags(Intent.FLAG_INCLUDE_STOPPED_PACKAGES)` 来发送自定义广播给未启动的 App。
- **系统广播**
系统发送的广播，Android 3.1 以上版本，未启动的 App 无法接收到系统广播。
- **粘性广播/粘性有序广播**
Android 5.0 以上版本已取消该类型广播。
- **有序广播**
广播接收者根据优先级来接收，并可以选择截断。
- **本地广播**
只有 App 内部可以发送或监听，更安全、更高效。
解决了接收到恶意广播或被窃听广播的问题。省去了使用普通广播时为了防窃听所加的以下步骤：
    1. 设置 android:exported=false
    2. 设定 permission
    3. 指定 Intent 的 packageName

#### 广播的两种注册形式？区别在哪？
静态注册、动态注册。
动态注册广播是观察者模式，跟随注册者的生命周期，需要反注册，不然会内存泄漏。
在接收普通广播时，动态注册的优先级高于静态注册。
在接收有序广播时，动态注册与静态注册设定的优先级相同时，动态注册的优先级更高。

[Home](../../README.md)