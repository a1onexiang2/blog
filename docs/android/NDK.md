[Home](../../README.md)

# Android

## NDK

#### JNI
JNI（Java Native Interface）是 Java 调用 Native 语言的一种特性，与 Android 无关。通过 JNI 可以在 Java 中调用本地其他类型语言（如 C、C++）的方法。

#### NDK
NDK（Native Development Kit）是用于 Android 开发 Native 代码的工具包，是在 Android 中实现 JNI 的途径。使用方法可以分为下面几个步骤：
1. 搭建 NDK 环境。
2. 配置项目 NDK 路径，主要是 `local.properties`、`gradle.properties`、`app/build.gradle`。
3. 以对应 Java 中声明的方法名定义 Native 方法：Java_包名_类名_方法名，包名中的 `.` 需要改为 `_`，所有的 `_` 需要改成 `_1`。
4. 创建 `Android.mk` 文件，并置于 `src/main/jni` 中。
5. 创建 `Application.mk` 文件，并置于 `src/main/jin` 中。
6. 编译 so 文件。
7. 将 so 文件置于 `src/main/jniLibs` 中。

上述流程后即可在 Java 中调用 Native 方法。


[Home](../../README.md)