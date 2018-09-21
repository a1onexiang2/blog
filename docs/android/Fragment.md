[Home](../../README.md)

# Android

## Fragment

#### Fragment 的生命周期？

`onAttach()` → `onCreate()` → `onCreateView()` → `onActivityCreated()` → `onStart()` → `onResume()` → `onPause()` → `onStop()` → `onDestroyView()` → `onDestroy()` → `onDetach()`
- **onAttach()**
表示 Fragment 与 Activity 建立关联
- **onCreateView()**
创建视图
- **onDestroyView()**
销毁视图
- **onDetach()**
表示 Fragment 与 Activity 解除关联

#### Fragment 与 Activity 关系？

Fragment 依附于 Activity，并托管生命周期给 Activity，无法独立使用。
Activity 里可以有多个 Fragment，一个 Fragment 可以被多个 Activity 重用。
Activity 可以管理 Fragment 的添加、删除、显示、隐藏、替换等操作。
Fragment 通讯

#### 何时会考虑使用 Fragment？

DialogFragment、ViewPager、BottomNavigator、Single Activity App。

[Home](../../README.md)