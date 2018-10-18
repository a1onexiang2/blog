[Home](../../README.md)  

# Java  

## Annotation  

Annotation 是 Java5 开始引入的特性。它提供了一种安全的类似于注释和 Java doc 的机制。  
注解相当于是一种嵌入在程序中的元数据，可以使用注解解析工具或编译器对其进行解析，也可以指定注解在编译期或运行期有效。这些元数据与程序业务逻辑无关，并且是供指定的工具或框架使用的。  

#### Meta Annotation  
Meta Annotation 指元注解，用于负责注解其他注解类。Java5 定义了 4 个标准的 Meta Annotation 类型，它们被用来提供对其它 Annotation 类型作说明。分别是：  
- **@Target**  
`@Target` 用于描述注解的使用范围，即被描述的注解可以用在什么地方。  
Annotation 可被用于 packages、types（类、接口、枚举、Annotation 类型）、类型成员（方法、构造方法、成员变量、枚举值）、方法参数和本地变量（如循环变量、catch 参数）。  
在 Annotation 类型的声明中使用了 `@Target` 可更加明晰其修饰的目标。`@Target` 可取值（ElementType）如下：  
    - CONSTRUCTOR：用于描述构造器  
    - FIELD：用于描述域  
    - LOCAL_VARIABLE：用于描述局部变量  
    - METHOD：用于描述方法  
    - PACKAGE：用于描述包  
    - PARAMETER：用于描述参数  
    - TYPE：用于描述类、接口（包括注解类型）或 enum 声明  
- **@Retention**  
`@Retention` 表示需要在什么级别保存该注释信息，用于描述注解的生命周期，即被描述的注解在什么范围内有效。  
某些 Annotation 仅出现在源代码中，而被编译器丢弃；而另一些却被编译在 class 文件中；编译在 class 文件中的 Annotation 可能会被虚拟机忽略，而另一些在 class 被装载时将被读取。`@Retention` 可取值如下：  
    - SOURCE：在源文件中有效，即源文件保留  
    - CLASS：在 class 文件中有效，即 class 保留  
    - RUNTIME：在运行时有效，即运行时保留  
- **@Documented**  
`@Documented` 用于描述其它类型的 Annotation 应该被作为被标注的程序成员的公共 API，因此可以被例如 javadoc 此类的工具文档化。  
`@Documented` 是一个标记注解，没有成员。  
- **@Inherited**  
`@Inherited` 是一个标记注解。如果一个使用了 `@Inherited` 修饰的 Annotation 类型被用于一个 class，则这个 Annotation 将被用于该 class 的子类。  

#### Java 内置 Annotation  
JavaSE 中内置三个标准 Annotation，定义在 java.lang 中：  
- **@Override**  
`@Override` 是一个标记型 Annotation，说明了被标注的方法覆盖了父类的方法，起到了断言的作用。  
如果给一个非覆盖父类方法的方法添加该 Annotation，编译器将报编译错误。  
- **@Deprecated**  
`@Deprecated` 是一个标记型 Annotation，说明被修改的元素已被废弃并不推荐使用，编译器会在该元素上加一条横线以作提示。  
该修饰具有一定的 “传递性” ：如果我们通过继承的方式使用了这个弃用的元素，即使继承后的元素（类，成员或者方法）并未被标记为 `@Deprecated`，编译器仍然会给出提示。  
- **@SuppressWarnnings**  
`@SuppressWarnnings` 用于通知 Java 编译器关闭对特定类、方法、成员变量、变量初始化的警告。  
通常当这种情况发生时，我们需要查找引起警告的代码，如果它真的表示错误，我们就需要纠正它。  
然而，有时我们无法避免这种警告，例如，我们使用必须和非 generic 的旧代码交互的 generic collection 类时，我们无法避免这个 unchecked warning，此时可以在调用的方法前增加 `@SuppressWarnnings` 通知编译器关闭对此方法的警告。  

#### Annotation、Interface 异同？  
Annotation 类型使用关键字 `@interface` 而非 `interface`。  
Annotation 的方法定义是受限制的。其方法必须声明为无参数、无异常抛出的。这些方法同时也定义了 Annotation 的成员——方法名即为成员名，而方法返回类型即为成员类型。方法返回类型必须为 Java 基础类型、Class 类型、枚举类型、Annotation 类型或者相应的一维数组。方法后面可以使用 default 关键字和一个默认数值来声明成员的默认值，null 不能作为成员默认值。  
成员一般不能是泛型，只有当其类型是 Class 时可以使用泛型，因为此方法能够用类型转换将各种类型转换为 Class。  
Annotation 和 interface 都可以定义常量、静态成员类型。interface 可以被实现或者继承，Annotation 不可以。  

[Home](../../README.md)  