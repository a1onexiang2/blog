[Home](../../README.md)

# Android

## Window
Android 手机中所有的视图都是通过 Window 来呈现的，像常用的 Activity、Dialog、PopupWindow、Toast，他们的视图都是附加在 Window 上的，可以说 Window 是 View 的直接管理者。
Window 是一个抽象类，唯一的实现类是 PhoneWindow。PhoneWindow 包含成员变量 DecorView。

#### Activity、Window、View 的关系
![image](https://user-images.githubusercontent.com/8423120/46188266-ae8f6800-c31a-11e8-8bae-8550862f8881.png)

#### `setContentView()`
`Activity.setContentView()` 实际上是调用 `PhoneWindow.setContentView()`。执行流程图如下：
![image](https://user-images.githubusercontent.com/8423120/46137177-afbd8800-c27b-11e8-93ee-5b11d975257b.png)

#### Window 类型
加窗口是通过 `WindowManagerGlobal.addView(View view, ViewGroup.LayoutParams params, Display display, Window parentWindow)` 来执行的。
在 LayoutParams 中有 2 个比较重要的参数: flags, type。
flags 表示 Window 的属性，通过 flags 可以控制 Window 的显示特性，例如 `FLAG_NOT_FOCUSABLE`、`FLAG_NOT_TOUCH_MODAL`、`FLAG_SHOW_WHEN_LOCKED` 等等。
type 表示 Window 的类型，Window 有三种类型：应用 Window、子 Window、系统 Window。应用类 Window 对应着一个 Activity；子 Window 不能单独存在，它需要附属在特定的父 Window 之中，比如常见的 PopupWindow 就是一个子 Window。有些系统 Window 是需要声明权限才能创建的 Window，比如 Toast 和系统状态栏这些都是系统 Window。
Window 是分层的，每个 Window 都有对应的 z 轴，从 1 叠加到 2999，z 轴大的会覆盖在 z 轴小的 Window 上面。
在三类 Window 中，应用 Window 的层级范围是 1~99。子 Window 的层级范围是 1000~1999，系统 Window 的层级范围是 2000~2999，这些层级范围对应着 WindowManager.LayoutParams 的 type 参数。如果想要 Window 位于所有 Window 的最顶层，那么采用较大的层级即可。但是系统层级的使用是需要声明权限的。

#### Window 创建过程
![image](https://user-images.githubusercontent.com/8423120/46188121-d205e300-c319-11e8-958b-bb39c2d12d01.png)
`onCreate()` Window 创建过程：
![image](https://user-images.githubusercontent.com/8423120/46188193-3fb20f00-c31a-11e8-9528-5c17adfdede0.png)
Activity 中 Window 创建过程：
![image](https://user-images.githubusercontent.com/8423120/46188242-915a9980-c31a-11e8-8244-57ce3255c2e6.png)


[Home](../../README.md)