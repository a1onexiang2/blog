[Home](../../README.md)

# Java

## Object

#### 基础类型

Type | Size (bits) | Minimum | Maximum | Example
-- | -- | -- | -- | --
byte | 8 | -2<sup>7</sup> | 2<sup>7</sup> – 1 | byte b = 100;
short | 16 | -2<sup>15</sup> | 2<sup>15</sup>– 1 | short s = 30_000;
int | 32 | -2<sup>31</sup> | 2<sup>31</sup>– 1 | int i = 100_000_000;
long | 64 | -2<sup>63</sup> | 2<sup>63</sup>– 1 | long l = 100_000_000_000_000;
float | 32 | -2<sup>-149</sup> | (2-2<sup>-23</sup>) * 2<sup>127</sup> | float f = 1.456f;
double | 64 | -2<sup>-1074</sup> | (2-2<sup>-52</sup>) * 2<sup>1023</sup> | double f = 1.456789012345678;
char | 16 | 0 | 2<sup>16</sup>– 1 | char c = ‘c’;
boolean | 1 | – | – | boolean b = true;

#### 包装类

基本数据类型 | 包装类 | 包装类转基本类型 | 基本类型转包装类
-- | -- | -- | --
byte | Byte | `Byte.valueOf(byte)` | `byteInstance.byteValue()`
short | Short | `Short.valueOf(short)` | `shortInstance.shortValue()`
int | Integer | `Integer.valueOf(int)` | `integerInstance.intValue()`
long | Long | `Long.valueOf(long)` | `longInstance.longValue()`
float | Float | `Float.valueOf(float)` | `floatInstance.floatValue()`
double | Double | `Double.valueOf(double)` | `doubleInstance.doubleValue()`
char | Character | `Character.valueOf(char)` | `charInstance.charValue()`
boolean | Boolean | `Boolean.valueOf(booleann)` | `booleanInstance.booleanValue()`

包装类为引用类型，使得基础类型有了 Object 的特征。

#### `==`、`equals()`

对于基础类型，== 比较的是它们的值。
对于引用类型，== 比较的是它们的内存地址。
`Object.equals()` 方法的默认实现直接调用了 ==。
可以复写 `equals()` 方法来进行复杂类型的值比较。
复写 `equals()` 方法的同时需要复写 `hashCode()` 方法，以便其它数据结构能正确识别。

#### String、StringBuffer、StringBuilder

String 在每次修改的时候会生成一个新的对象，在操作频率高的情况下效率低下。
StringBuffer、StringBuilder 实现大致相同，内部使用 char[] 来储存字符串，在 `toString()` 之后才生成字符串对象。
StringBuffer 线程安全。
StringBuilder 线程不安全，在单线程下效率更高。
注意 StringBuffer、StringBuilder 在扩容的时候可能会导致 OOM 的问题。

#### Serializable、Parcelable

- **Serializable**<br>
    - 空接口，不需要实现方法。
    - 定义全局唯一的 serialVersionUID。序列化时会把当前类的 serialVersionUID 写入到序列化文件中，当反序列化时需要 serialVersionUID 一致，否则会失败。
    - 不指定 serialVersionUID 时系统会自动生成，文件发生任何修改都会导致生成的 UID 不同。
    - 通过 I/O 进行储存与读取。
    - 序列化、反序列化过程中会产生大量临时变量，引起 GC。
    - 内部实现使用了反射，效率低下。
    - transient 标识的参数不会参与序列化、反序列化过程。
    - 反序列化后的对象是新创建的，与原对象内存地址不同。
- **Parcelable**<br>
    - 需要实现 `writeToParcel()`、`createFromParcel()`、`describeContents()`、`CREATOR`。
    - 直接在内存（共享内存）上进行储存于读取。
    - 将变量进行分解存储。
    - 反序列化后的对象是新创建的，与原对象内存地址不同。

#### String → Integer

通过 char 逐个分析。

[Home](../../README.md)