[Home](../../README.md)

# Android

## Animation

#### Animation 种类
主要有三种：View Animation、Drawable Animation、Property Animation。
- **View Animation**<br>
补间动画，动画执行之后并没有改变 View 的真实布局属性。
- **Drawable Animation**<br>
帧动画，把一个个 Drawable 逐帧播放来表现动画。
- **Property Animation**<br>
属性动画，主要有 ObjectAnimator、ValueAnimator、TimeAnimator、AnimatorSet。

#### Property Animation
- **ValueAnimator**<br>
ValueAnimator 只是动画计算管理驱动，没有设置对应的属性，需要设置 updateListener 并更新属性才回生效。
- **ObjectAnimator**<br>
继承自 ValueAnimator，是比较常用的 Animator。
使用 ObjectAnimator 需要目标对象指定的属性提供 getter、setter 方法。
- **AnimatorSet**<br>
动画集合，把多个动画组合成一个组合，并可设置动画的时序关系。
- **PropertyValuesHolder**<br>
多属性动画同时工作管理类。需要同时修改多个属性时可以用到此类。
- **Evaluators**<br>
估值器。Evaluators 用于计算一个属性值。它们通过 Animator 提供的动画的起始和结束值去计算一个动画的属性值。
- **Interpolators**<br>
插值器。



[Home](../../README.md)