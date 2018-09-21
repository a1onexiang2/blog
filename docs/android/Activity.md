[Home](../../README.md)

# Android

## Activity

#### Activity 的生命周期？

`onCreate()` / `onRestart()` → `onStart()` → `onResume()` → `onPause()` → `onStop()` → `onDestroy()`
- **onCreate()**
创建 Activity，可根据参数 savedInstanceState 恢复一些状态。
- **onStart()**
表示 Activity 可见。
- **onResume()**
表示 Activity 可见并获取焦点，可以与用户进行交互。
- **onPause()**
与 `onResume()` 对应，表示 Activity 失去焦点，用户无法交互。
- **onStop()**
与 `onStart()` 对应，表示 Activity 不再可见。
- **onDestroy()**
销毁 Activity。
- **onRestart()**
表示 Activity 从不可见状态变成可见状态。

#### Activity A 启动另一个 Activity B 会回调哪些方法？如果 Activity B 是透明的呢？如果启动的是一个 Dialog 呢？

A 启动 B：`A.onPause()` → `B.onCreate()` → `B.onStart()` → `B.onResume()` → `A.onStop()`
A 启动透明 B： `A.onPause()` → `B.onCreate()` → `B.onStart()` → `B.onResume()`
B 返回 A：`B.onPause()` → `A.onRestart()` → `A.onStart()` → `A.onResume()` → `B.onStop()` → `B.onDestroy()`
透明 B 返回 A：`B.onPause()` → `A.onResume()` → `B.onStop()` → `B.onDestroy()`
Dialog 控件不会对 Activity 生命周期产生影响

#### `onSaveInstanceState()`、`onRestoreInstanceState()`

非用户主动销毁情况下，会调用 `onSaveInstanceState()`，用于储存一些临时变量与控件状态。
`onSaveInstanceState()` 调用在 `onStop()` 前，与 `onPause()` 没有时序关系。
`onRestoreInstanceState()` 被调用的前提是的确发生了非正常销毁，并不与 onSaveInstanceState() 成对调用。
`onRestoreInstanceState()` 调用在 `onStart()` 之后，`onResume()` 之前。

#### 如何避免配置改变时 Activity 重建？

在 AndroidManifest.xml 中配置 Activity 中的 `android:configChanges` 属性。

#### Activity 启动模式？

分为四种：standard，singleTop，singleTask，singleInstance。
可以在 `AndroidManifest.xml` 中配置 Activity 中的 `android:launchMode` 属性。也可以调用 `Intent.addFlags(flag)` 来指定。
- **standard**
默认模式，每次启动开启一个新实例，处于启动它的任务栈中。
- **singleTop**
为栈顶复用模式，需要启动时已经有实例位于栈顶则不会创新新实例，回调 onNewIntent() 方法。
- **singleTask**
为栈内复用模式，需要启动时已经有实例位于栈内则不会创新新实例，回调 onNewIntent() 方法，并且清除任务栈内处于该 Activity 之上的 Activities。
- **singleInstance**
为单实例模式，位于独立任务栈，有且只有该 Activity。
`AndroidManifest.xml` 中配置 Activity 中的 `android:taskAffinity` 属性可以指定任务栈，默认任务栈为包名。
ApplicationContext 启动 standard/singleTop/singleTask Activity 会报错，因为没有任务栈，Service、BoardcastReceiver 同理。

#### 如何启动其他应用的 Activity？

显示调用，`Intent.setClassName(packageName, className)`。
隐示调用，设置 action/category/data。
需要注意添加 `Intent.FLAG_ACTIVITY_NEW_TASK`。

#### Activity 的启动过程？

- 程序进程（客户端）
    Activity.startActivity()
    Activity.startActivityForResult()
    ContextImpl.execStartActivity()
    Instrumentation.startActivity()
    ActivityManagerProxy.startActivity()
- 系统进程（服务端）
    1. 预启动
    ActivityManagerService.startActivity()
    ActivityStack.startActivityMayWait()
    ActivityStack.startActivityLocked()
    ActivityStack.startActivityUncheckedLocked()
    ActivityStack.startActivityLocked()（重载）
    ActivityStack.resumeTopActivityLocked()
    1. 暂停
    ActivityStack.startPausingLocked()
    ApplicationThreadProxy.schedulePauseActivity()
    ActivityThread.handlePauseActivity()
    ActivityThread.performPauseActivity()
    ActivityManagerProxy.activityPaused()
    completePausedLocked()
    1. 启动应用程序进程
    第二次进入 ActivityStack.resumeTopActivityLocked()
    ActivityStack.startSpecificActivityLocked()
    startProcessLocked()
    startProcessLocked()（重载）
    Process.start()
    1. 加载应用程序 Activity
    ActivityThread.main()
    ActivityThread.attach()
    ActivityManagerService.attachApplication()
    ApplicationThread.bindApplication()
    ActivityThread.handleBindApplication()
    1. 显示 Activity
    ActivityStack.realStartActivityLocked()
    ApplicationThread.scheduleLaunchActivity()
    ActivityThead.handleLaunchActivity()
    ActivityThread.performLaunchActivity()
    ActivityThread.handleResumeActivity()
    ActivityThread.performResumeActivity()
    Activity.performResume()
    ActivityStack.completeResumeLocked()
    1. Activity Idle 状态的处理
    2. 停止源 Activity
    ActivityStack.stopActivityLocked()
    ApplicationThreadProxy.scheduleStopActivity()
    ActivityThread.handleStopActivity()
    ActivityThread.performStopActivityInner()

[Home](../../README.md)