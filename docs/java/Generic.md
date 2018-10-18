[Home](../../README.md)

# Java

## Generic

泛型，也叫参数化类型。即将类型由原来的具体的类型参数化，类似于方法中的变量参数，此时类型也定义成参数形式（可以称之为类型形参），然后在使用/调用时传入具体的类型（类型实参）。
泛型的本质是为了在不创建新的类型的情况下，通过指定的不同类型来控制形参具体限制的类型。也就是说在泛型使用过程中，操作的数据类型被指定为一个参数，这种参数类型可以用在类、接口和方法中，分别被称为泛型类、泛型接口、泛型方法。
泛型只在编辑阶段产生作用，在编译后会擦除泛型，泛型信息不会进入运行阶段。
泛型的类型参数只能是类类型，不能是简单类型。

#### 静态泛型方法
静态方法无法访问类上定义的泛型。如果静态方法操作的引用数据类型不确定的时候，必须要将泛型定义在方法上。
```java
public class StaticGenerator<T> {
    /**
     * 如果在类中定义使用泛型的静态方法，需要添加额外的泛型声明（将这个方法定义成泛型方法）
     * 即使静态方法要使用泛型类中已经声明过的泛型也不可以。
     * 如：public static void show(T t){..},此时编译器会提示错误信息：
     * "StaticGenerator cannot be refrenced from static context"
     */
    public static <T> void show(T t){
    }
}
```

#### Kotlin 内联
Java 中需要显示传入 Class<T> 来拿到泛型具体的 class 对象；Kotlin 可以通过内联函数实现
```kotlin
inline fun <reified T> Gson.fromJson(json: String): T{
    return fromJson(json, T::class.java)
}
```
inline 和 reified 缺一不可。

[Home](../../README.md)