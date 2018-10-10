[Home](../../README)

# Android

## View

#### LayoutInflater
通过 `Context.getSystemService(Context.LAYOUT_INFLATER_SERVICE)` / `LayoutInflater.from(context)` 来获取。
内部通过 XmlPullParser 来解析 xml 文件，并通过反射创建 View。
如果 root 参数不为空，attachToRoot 参数默认为 true，创建的 View 会被添加到 root 中。
如果 root 为 null 或 attachToRoot 为 false 时，创建的 View 最外层的 layout_width 和 layout_height 会失效，因为没有添加到视图中。

#### AsyncLayoutInflater
AsyncLayoutInflater 是来帮助做异步加载 layout 的。
默认情况下 `setContentView()` 函数是在 UI 线程执行的，其中有一系列的耗时动作：XML 的解析、View 的反射创建等过程同样是在 UI 线程执行的，AsyncLayoutInflater 把这些过程以异步的方式执行，保持 UI 线程的高响应。

#### 绘制流程

- **onMeasure(widthMeasureSpec, heightMeasureSpec)**
`onMeasure()` 方法用于测量控件的大小。
`ViewRootImpl.performTraversals()` 方法会调用不可复写的 `measure(widthMeasureSpec, heightMeasureSpec)` 方法来测量 View 大小，其中参数由父布局传递。并在其中调用 `onMeasure()` 方法。
ViewGroup 实现了 `measureChildren()` 方法来调用每个 ChildView 的 `measure()` 方法。
- **onLayout(changed, l, t, r, b)**
`onLayout()` 方法决定控件所在的位置。
`ViewRootImpl.performTraversals()` 方法会调用 `layout(l, t, r, b)` 方法来决定 View 所在的位置，并在其中调用 `onLayout()` 方法。
View 的 `onLayout()` 方法是空实现，ViewGroup 的 `onLayout()` 方法是抽象的，需要调用每个 ChildView 的 `layout()` 方法。
- **onDraw(canvas)**
`onDraw()` 方法绘制控件。
`ViewRootImpl.performTraversals()` 方法会调用 `draw(canvas)` 方法来进行 View 的绘制。并在其中调用 `onDraw(canvas)` 方法。
View 的 `onDraw()` 方法是空实现。

#### `requestLayout()`、`invalidate()`、`postInvalidate()`

- **requestLayout()**
从 View 开始一路往上请求 `requestLayout()` 并最终至 `ViewRootImpl.requestLayout()`。
对 View 重新进行测量、布局、绘制。
- **invalidate()**
为 View 添加一个标记位后不断向父容器请求刷新，父容器通过计算得出自身需要重绘的区域并上报，直到传递至 ViewRootImpl，最终触发 `performTraversals()` 方法，进行开始 View 树重绘流程 (只绘制需要重绘的视图)。
- **postInvalidate()**
与 `invalidate()` 类似，但是可以从非 UI 线程发起。

如果 View 确定自身不再适合当前区域，例如 LayoutParams 发生改变，需要父布局对其进行重新测量、布局、绘制时，需要调用 `requestLayout()`。而 `invalidate()` 则是刷新当前 View，使当前 View 进行重绘，不会执行测量、布局流程，因此如果 View 只需要重绘而不需要测量，布局的时候，使用 `invalidate()` 方法比 `requestLayout()` 方法更高效。

#### 视图状态

常见的状态有 enabled、focused、pressed、selected 等等。
在状态发生改变的时候会调用 `View.drawableStateChanged()` 方法，最终会调用到 `invalidateDrawable()` 方法，最终触发 `invalidate()` 进行重绘。

#### MotionEvent、事件分发机制、手势冲突

- **MotionEvent**
每当用户在屏幕上进行动作时，会生成一个与之对应的 MotionEvent。
`getX()`、`getY()` 方法可以获取事件距离消费这个事件的控件相对位置。
`getRawX()`、`getRawY()` 方法可以获取事件在屏幕中的绝对位置。
ACTION_DOWN → ACTION_MOVE → ... → ACTION_MOVE → ACTION_UP/ACTION_CANCEL 形成一个事件流。
关于多点触控，`getActionIndex()` 方法可以获取事件的 index，并通过 `getPointerId(index)` 来获取事件流 ID，同一个事件流的 ID 是相同的。
- **事件分发机制**
详细流程见下图
![image](http://gityuan.com/images/touch/touch1.jpg)
不论 View 自身是否注册点击事件，只要 View 是可点击的就会消费事件。
ViewGroup 和 ChildView 都注册了事件监听器时，由 ChildView 消费。
一次触摸流程中产生事件应该被同一 View 消费，全部接收或者全部拒绝。只要接受 ACTION_DOWN 就意味着接受所有的事件，拒绝 ACTION_DOWN 则不会收到后续内容。
事件被拦截时会触发 ACTION_CANCEL 事件，如果事件流中途被拦截，后续的事件也将被拦截。
- **手势冲突**
基本全都可以通过事件分发机制解决。

#### Canvas
详见[这里](./Canvas.md)

#### Scroller

Scroller 用于实现 View 的平滑滑动。
`Scroller.startScroll(int startX, int startY, int dx, int dy, int duration)` 方法会计算出每一帧 View 要移动的位置并进行重绘。
复写 `View.computeScroll()` 方法便可实现平滑移动，具体例子如下：
```java
@Override
public void computeScroll() {
    if (mScroller.computeScrollOffset()) {
        scrollTo(mScroller.getCurrX(),mScroller.getCurrY());
        postInvalidate();
    }
    super.computeScroll();
}
```

#### SurfaceView
为什么使用 SurfaceView，主要有两点：
如果屏幕刷新频繁，`View.onDraw()` 方法会被频繁的调用，`View.onDraw()` 方法执行的时间过长，会导致掉帧，出现页面卡顿。SurfaceView 采用了双缓冲技术，提高了绘制的速度，可以缓解这一现象。
`View.onDraw()` 方法是运行在主线程中的，会轻微阻塞主线程，对于需要频繁刷新页面的场景，如果 `View.onDraw()` 方法中执行的操作比较耗时，会导致主线程阻塞，用户事件的响应也会受到影响，响应速度下降，影响了用户的体验。SurfaceView 可以在自线程中更新 UI，不会阻塞主线程，提高了响应速度。

双缓冲技术是游戏开发中的一个重要的技术。当一个动画争先显示时，程序又在改变它，前面还没有显示完，程序又请求重新绘制，这样屏幕就会不停地闪烁。而双缓冲技术是把要处理的图片在内存中处理好之后，再将其显示在屏幕上。双缓冲主要是为了解决反复局部刷屏带来的闪烁。把要画的东西先画到一个内存区域里，然后整体的一次性画出来。

采用了 MVC 设计思想，SurfaceHolder.Surface 担任了 Model 角色、SurfaceHolder 担任了 Controller 角色、SurfaceView 担任了 View 角色。

#### Animation
主要有三种：View Animation、Drawable Animation、Property Animation。
- **View Animation**
补间动画，动画执行之后并没有改变 View 的真实布局属性。
- **Drawable Animation**
帧动画，把一个个 Drawable 逐帧播放来表现动画。
- **Property Animation**
属性动画，主要有 ObjectAnimator、ValueAnimator、TimeAnimator、AnimatorSet。

#### Property Animation
- **ValueAnimator**
ValueAnimator 只是动画计算管理驱动，没有设置对应的属性，需要设置 updateListener 并更新属性才回生效。
- **ObjectAnimator**
继承自 ValueAnimator，是比较常用的 Animator。
使用 ObjectAnimator 需要目标对象指定的属性提供 getter、setter 方法。
- **AnimatorSet**
动画集合，把多个动画组合成一个组合，并可设置动画的时序关系。
- **PropertyValuesHolder**
多属性动画同时工作管理类。需要同时修改多个属性时可以用到此类。
- **Evaluators**
估值器。Evaluators 用于计算一个属性值。它们通过 Animator 提供的动画的起始和结束值去计算一个动画的属性值。
- **Interpolators**
插值器。

[Home](../../README)