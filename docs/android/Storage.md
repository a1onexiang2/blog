[Home](../../README)

# Android

## Storage

Android 内容存储主要有五种方式：文件存储、SharedPreferences、SQLite 数据库存储、ContentProvider、网络存储。

#### 文件存储
所有的 Android 设备都有两块存储区域：Internal Storage 和 External Storage。它们的名称来源于早期的 Android 系统，那时候大家的手机都内置（Permanent）一块较小存储板（Internal Storage），并配上一个的外置储存卡（External Storage）。
后来部分手机开始将最初定义的 Internal Storage 分成 Internal 和 External 两部分。这样一来即使没有外置储存，手机也有 Internal 和 External 两块存储区域。当手机可以插入外置储存卡的时候，外置储存卡被称作 Physical External Storage。
Internal Storage 永远可用 (Permanent)。
External Storage 可能不可用，最典型的当设备作为 USB 存储被 mount 时不可用。
- **内部存储**
在设备内存中存储私有数据。
默认情况下，保存到内部存储的文件是应用的私有文件，其他应用（和用户）不能访问这些文件。当应用被卸载时，这些文件也会被移除。
    调用方法 | 等价路径 | 是否跟随应用
    -- | -- | --
    `Environment.getRootDirectory()` | `/system` | 否
    `Environment.getDataDirectory()` | `/data` | 否
    `Context.getPackageCodePath()` | `/data/app/com.my.app-1.apk` | 是
    `Context.getPackageResourcePath()` | `/data/app/com.my.app-1.apk` | 是
    `Context.getCacheDir()` | `/data/data/com.my.app/cache` | 是
    `Context.getDatabasePath("test")` | `/data/data/com.my.app/databases/test` | 是
    `Context.getDir("test", Context.MODE_PRIVATE)` | `/data/data/com.my.app/app_test` | 是
    `Context.getFilesDir()` | `/data/data/com.my.app/files` | 是
- **外部存储**
该存储可能是可移除的存储介质（例如 SD 卡）或内部（不可移除）存储。
保存到外部存储的文件是全局可读取文件，而且，在计算机上启用 USB 大容量存储以传输文件后，可由用户修改这些文件。
要使用外部存储，必须获取 READ_EXTERNAL_STORAGE 或 WRITE_EXTERNAL_STORAGE 系统权限。
在使用外部存储之前，应始终调用 `Environment.getExternalStorageState()` 以检查介质是否可用。
`Context.getExternalFilesDir()` 获取的路径是私有的。当应用被卸载时，此目录及其内容将被删除。系统扫描程序不会读取这些目录中的文件。
    调用方法 | 等价路径 | 是否跟随应用
    -- | -- | --
    `Environment.getDownloadCacheDirectory()` | `/cache` | 否
    `Environment.getExternalStorageDirectory()` | `/sdcard` | 否
    `Environment.getExternalStoragePublicDirectory("test")` | `/sdcard/test` | 否
    `Environment.getExternalStoragePublicDirectory(DIRECTORY_MUSIC)` | `/sdcard/Music` | 否
    `Environment.getExternalStoragePublicDirectory(DIRECTORY_PODCASTS)` | `/sdcard/Podcasts` | 否
    `Environment.getExternalStoragePublicDirectory(DIRECTORY_RINGTONES)` | `/sdcard/Ringtones` | 否
    `Environment.getExternalStoragePublicDirectory(DIRECTORY_ALARMS)` | `/sdcard/Alarms` | 否
    `Environment.getExternalStoragePublicDirectory(DIRECTORY_NOTIFICATIONS)` | `/sdcard/Notifications` | 否
    `Environment.getExternalStoragePublicDirectory(DIRECTORY_PICTURES)` | `/sdcard/Pictures` | 否
    `Environment.getExternalStoragePublicDirectory(DIRECTORY_MOVIES)` | `/sdcard/Movies` | 否
    `Environment.getExternalStoragePublicDirectory(DIRECTORY_DOWNLOADS)` | `/sdcard/Download` | 否
    `Environment.getExternalStoragePublicDirectory(DIRECTORY_DCIM)` | `/sdcard/DCIM` | 否
    `Environment.getExternalStoragePublicDirectory(DIRECTORY_DOCUMENTS)` | `/sdcard/Documents` | 否
    `Context.getExternalCacheDir()` | `/sdcard/Android/data/com.my.app/cache` | 是
    `Context.getExternalFilesDir(null)` | `/sdcard/Android/data/com.my.app/files` | 是
    `Context.getExternalFilesDir("test")` | `/sdcard/Android/data/com.my.app/files/test` | 是

#### SharedPreferences
SharedPreferences 是 Android 平台上一个轻量级的存储类，用来保存应用程序的各种配置信息，其本质是一个以 key-value 方式保存数据的 xml 文件，其文件保存在 `/data/data/shared_prefs` 目录下。
内部实现了内存缓存，在初始化单例的时候会加载并解析 xml 文件后缓存在内存中，在查询时会直接读取缓存，修改时也会同步更新缓存。
可以注册 OnSharedPreferenceChangeListener 方法来监听特定值的变化。
有两个提交方法：`commit()` 和 `apply()`。`commit()` 方法会同步执行更新内存缓存和写入文件的操作，并会返回提交结果。`apply()` 方法会把数据同步更新至内存缓存，然后异步执行文件写入操作。
在使用上有几个值得注意的点：
1. 不要储存大字段 key 或 value。SharedPreferences 由于加载并解析整个文件至内存缓存中并且常驻，所以不要在其中储存过大的数据，在解析时会导致卡顿，缓存也会导致占用内存过多。
2. 不要储存 JSON 字符串。JSON 在储存时会进行转义，解析 xml 时每次碰到转义符时都会有额外的处理开销。
3. 不要多次 `edit()`。每次调用 `edit()` 都会创建一个 Editor 对象，但毫无必要。
4. 不要在主线程调用 `commit()`。`commit()` 方法会同步执行写入文件的操作，有可能卡顿界面。
5. 不要多次 `apply()`。`apply()` 方法排列在一个单线程的线程池队列中，若队列仍在执行，会阻塞到 Activity.onStop() 的执行。
6. 不要跨进程使用。SharedPreferences 内部实现不支持跨进程使用。
7. OnSharedPreferenceChangeListener 不要使用匿名内部类实现，因为内部使用了弱引用，容易被系统回收。

#### SQLite
Android 内部集成了 SQLite。通过继承 SQLiteOpenHelper 来使用。数据库文件存储在 `/data/data/com.my.app/databases` 中。
一般会使用第三方库来实现数据库功能。常见的第三方库有：GreenDao、LitePal、LiteOrm 等等。

#### ContentProvider
ContentProvider 主要用于不同应用间的数据共享，需要自己实现数据的增删改查操作才能使用。

#### 网络储存
即数据储存于服务器端，通过网络通讯来获取。

[Home](../../README)