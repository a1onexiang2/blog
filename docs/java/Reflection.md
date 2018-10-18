[Home](../../README.md)  

# Java  

## Reflection  

反射（Reflection）是 Java 的特征之一，它允许运行中的 Java 程序获取自身的信息，并且可以操作类或对象的内部属性。  
简而言之，通过反射，我们可以在运行时获得程序或程序集中每一个类型的成员和成员的信息。  
程序中一般的对象的类型都是在编译期就确定下来的，而 Java 反射机制可以动态地创建对象并调用其属性，这样的对象的类型在编译期是未知的。所以我们可以通过反射机制直接创建对象，即使这个对象的类型在编译期是未知的。  
反射的核心是 JVM 在运行时才动态加载类或调用方法/访问属性，它不需要事先（写代码的时候或编译期）知道运行对象是谁。  
由于反射会额外消耗一定的系统资源，因此如果不需要动态地创建一个对象，那么就不需要用反射。  
反射调用方法时可以忽略权限检查，因此可能会破坏封装性而导致安全问题。  

#### Class  
有三种方法可以获取 Class 对象，分别为：使用 Class 类的 forName 静态方法 `Class.forName(name)`、直接获取类的 class 静态对象 `Object.class` 或调用某个实例的 `getClass()` 方法。  
可以调用 `Class.isInstance(object)` 来判断是否是某个类的实例。  
有两种方法可以通过 Class 对象创建实例：调用 `Class.newInstance()` 方法来创建一个新的实例或通过 `Class.getConstructor(...class)` 方法获取构造器对象，再调用 `constructor.newInstance(...args)` 来创建实例。  

#### Method  
获取某个 Class 对象的方法集合，主要有以下几个方法：  
`getDeclaredMethods()` 方法返回类或接口声明的所有方法，包括公共、保护、默认（包）访问和私有方法，但不包括继承的方法。  
`getMethods()` 方法返回某个类的所有 public 方法，包括其继承类的公用方法。  
`getMethod(methodName, ...args)` 方法返回一个特定的方法，其中第一个参数为方法名称，后面的参数为方法的参数对应 Class 的对象。  
`getDeclaredMethod(methodName, ...args)` 方法返回一个特定的方法。  
通过调用 `Method.invoke(object, ...args)` 方法可以调用获取到的 Method。  

#### Field  
获取某个 Class 对象的变量集合，主要有以下几个方法：  
`getDeclaredFields()` 方法返回类或接口的所有变量，包括公共、保护、默认（包）访问和私有变量，但不包括继承的变量。  
`getFields()` 方法返回某个类的所有公用 public 变量，包括其继承类的公用变量。  
`getField(fieldName, ...args)` 方法返回一个特定的变量，其中第一个参数为方法名称，后面的参数为方法的参数对应 Class 的对象。  
`getDeclaredField(fieldName, ...args)` 方法返回一个特定的变量，其中第一个参数为方法名称，后面的参数为方法的参数对应 Class 的对象。  
通过调用 `Field.get(object)` 方法可以获取变量的值。  
通过调用 `Field.set(object, value)` 方法可以改变变量的值。  

[Home](../../README.md)  