[Home](../../README)

# Android

## Service

#### Service 的生命周期？

`onCreate()` → `onStartCommand()` / `onBind()` → (`onUnbind()` →) `onDestroy()`
`onStartCommand()` 在 `Context.startService()` 的场合被调用，`onBind()` 在 `Activity.bindService()` 的场合被调用，两者不冲突，可共存。
`Context.stopService()` / `Service.stopSelf()` 会停止服务，`Activity.unbindService()` 会解绑服务。
`onDestroy()` 只有在服务停止并且解除绑定后才会触发。

#### Service 如何和 Activity 进行通信？

- 通过 `Activity.bindService()` 获取 IBinder
- 通过 BoardcastReceiver
- 通过 Messager
- 通过 AIDL

#### 用过哪些系统 Service？

![image](https://user-images.githubusercontent.com/8423120/46723755-2494b700-ccab-11e8-9c05-5cf52548b75a.png)
- LAYOUT_INFLATER_SERVICE
- CONNECTIVITY_SERVICE
- VIBRATOR_SERVICE
- AUDIO_SERVICE
- TELEPHONY_SERVICE
- CLIPBOARD_SERVICE
- APPWIDGET_SERVICE
- NFC_SERVICE
- BLUETOOTH_SERVICE
- CAMERA_SERVICE

#### 关于 Service 耗时操作？如果非要可以怎么做？

Service 运行在主线程中，不能进行耗时操作，超过 20 秒会 ANR。需要开启异步线程来执行耗时操作。
例如 Thread、AsyncTask 等等，或者直接继承 IntentService。

#### Foreground Service 是什么？

前台服务，拥有更高的优先级，不会被系统杀死。
通过 `Activity.startForegroundService()` / `NotificationManager.startServiceInForeground()` 来启动前台服务。
在 Android O 以上后台应用无法启动前台服务。
需要在 5 秒内调用 `Service.startForeground()` 来显示通知栏，不然会产生 ANR。

#### Service 保活？

- 前台服务
- 相互唤醒
- 利用系统漏洞
    - SDK 18 以下，直接在 `Service.startForeground()` 中传入空的 Notification 即可。
    - SDK 18 及以上，可以启动一个 ID 一样的 InnerService 并停止。

[Home](../../README)