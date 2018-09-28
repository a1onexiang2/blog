[Home](../../README.md)

# Java

## Design Patterns
Java 设计模式。

#### 单例模式
![](https://user-images.githubusercontent.com/8423120/46202059-5d966880-c348-11e8-8a12-c07b8ec3c681.png)
在内存中有且只有一个实例，构造函数不对外开放，通过静态方法或枚举返回。单例模式可以避免对资源的多重占用。使用单例模式时，需要考虑多线程环境下如何保证安全，以及反序列化时如何确保不创建新对象。
他有三要素：
- 私有的构造方法
- 指向自己实例的私有静态引用
- 以自己实例为返回值的静态的公有的方法
有以下常见实现方法：
- 饿汉模式
在类加载时直接完成实例的初始化操作。避免了多线程同步的问题，但是会导致类加载变慢。
`Singleton instance = new Singleton();`
- 懒汉模式
在类加载时不进行初始化，在首次调用时进行初始化。多线程情况下会出现问题。
    ```java
    if (instance == null) {
        instance = new Singleton();
    }
    return instance;
    ```
- 双重检验锁
能在需要使用时才创建实例，在已经初始化后可以直接返回，而且在一定程度上解决了线程同步问题。
但是新建对象并不是原子操作，在特定情况下仍然会有线程同步问题，可能会创建多个对象，也可能已分配内存但还没执行构造函数时直接返回导致抛出 NullPointerException 。添加 volatile 关键字可以解决该问题，但会导致效率变低。
    ```java
    if (instance == null) {
        synchronized(Singleton.class) {
            if (instance == null) {
                instance = new Singleton();
            }
        }
    }
    return instance;
    ```
- 静态内部类
延迟加载，线程安全，也减少了内存消耗。
    ```java
    private static class SingletonHolder {
        private static Singleton instance = new Singleton();
    }
    ```
    上述情况在反序列化时都会创建新实例，需要额外实现才能复用原本的对象。反序列化时会通过一个特别的钩子函数 readResolve() 去创建类的一个新的实例，可以进行如下修改：
    ```java
    private Object readResolve() throws ObjectStreamException{
        return instance;
    }
    ```
- 枚举单例
枚举线程安全，自带反序列化机制，即使面对反序列化或反射攻击时也不会多次实例化，但是内存占用更多。
    ```java
    public enum SingletonEnum {
        instance;

        SingletonEnum() {
        }
    }
    ```

例子：Context.getSystemService(serviceName)...

#### 工厂方法模式
![](https://user-images.githubusercontent.com/8423120/46202021-3f306d00-c348-11e8-8de5-c26ee395a83d.png)
在任何需要生成复杂对象的地方，都可以使用工厂方法模式，复杂对象适合使用工厂方法模式，可以简单创建对象的类无需使用工厂方法模式。
工厂方法模式有四个角色：
- Product（抽象产品类）：要创建的复杂对象，定义对象的公共接口。
- ConcreteProduct（具体产品类）：实现 Product 接口。
- Factory（抽象工厂类）：该方法返回一个 Product 类型的对象。
- ConcreteFactory（具体工厂类）：返回 ConcreteProduct 实例。
例子：Iterator、ThreadFactory...

#### 抽象工厂模式
![](https://user-images.githubusercontent.com/8423120/46202080-6dae4800-c348-11e8-8cb8-bdaba6659ec0.png)
是工厂方法模式的升级版本，他用来创建一组相关或者相互依赖的对象。在工厂方法模式中具体工厂负责生产具体的产品，每一个具体工厂对应一种具体产品，工厂方法具有唯一性。抽象工厂模式则可以提供多个产品对象，而不是单一的产品对象。
抽象工厂模式有四个角色：
- AbstractProduct（抽象产品类）：定义产品的公共接口。
- ConcreteProduct（具体产品类）：定义产品的具体对象，实现抽象产品类中的接口。
- AbstractFactory（抽象工厂类）：定义工厂中用来创建不同产品的方法。
- ConcreteFactory（具体工厂类）：实现抽象工厂中定义的创建产品的方。

#### 建造者模式
![](https://user-images.githubusercontent.com/8423120/46202608-e06bf300-c349-11e8-9f4c-758410ab564d.png)
一步步创建一个复杂对象的创建型模式，它允许用户在不知道内部构建细节的情况下，可以更精细的控制对象的构造流程。该模式是为了将构造复杂对象的过程和它的部件解耦，使得构建过程和部件的表示隔离开来。
建造者模式有四个角色：
- Product（产品类）：要创建的复杂对象。在本类图中，产品类是一个具体的类，而非抽象类。实际编程中，产品类可以是由一个抽象类与它的不同实现组成，也可以是由多个抽象类与他们的实现组成。
- Builder（抽象建造者）：创建产品的抽象接口，一般至少有一个创建产品的抽象方法和一个返回产品的抽象方法。引入抽象类，是为了更容易扩展。
- ConcreteBuilder（实际的建造者）：继承 Builder 类，实现抽象类的所有抽象方法。实现具体的建造过程和细节。
- Director（指挥者类）：分配不同的建造者来创建产品，统一组装流程。
例子：AlertDialog.Builder、Notification.Builder...

#### 原型模式
![](https://user-images.githubusercontent.com/8423120/46202658-05606600-c34a-11e8-9801-0a3d0d1b7951.png)
一般指有一个样板实例，可以从这个样板对象中复制出一个内部属性一致的对象，即“克隆”，被复制的实例即原型。原型模式多用于创建复杂或构造耗时的实例，这种情况下复制一个已经存在的实例可使程序运行更加高效。
原型模式需要实现 Cloneable 接口并复写 `Object.clone()` 方法。
原型模式有三个角色：
- Prototype（抽象原型类）：抽象类或者接口，用来声明 clone 方法。
- ConcretePrototype1、ConcretePrototype2（具体原型类）：即要被复制的对象。
- Client（客户端类）：即要使用原型模式的地方。
例子：Bundle、Intent...

#### 策略模式
![](https://user-images.githubusercontent.com/8423120/46203784-6b9ab800-c34d-11e8-9279-b4dbe9775729.png)
定义一系列的算法，把每一个算法封装起来, 并且使它们可相互替换。策略模式模式使得算法可独立于使用它的客户而独立变化。
策略模式有三个角色：
- Stragety(抽象策略类)：抽象类或接口，提供具体策略类需要实现的接口。
- ConcreteStragetyA、ConcreteStragetyB（具体策略类）：具体的策略实现，封装了相关的算法实现。
- Context（环境类）：用来操作策略的上下文环境。
例子：Adapter、Interpolator...

#### 状态模式
![](https://user-images.githubusercontent.com/8423120/46203879-c03e3300-c34d-11e8-890f-a3e30fa9d411.png)
当一个对象的内在状态改变时允许改变其行为，这个对象看起来像是改变了其类。
状态模式有三个角色：
- State（抽象状态角色）：抽象类或者接口，定义对象的各种状态和行为。
- ConcreteState（具体状态角色）：实现抽象角色类，定义了本状态下的行为，即要做的事情。
- Context（环境角色）：定义客户端需要的接口，并且负责具体状态的切换。

#### 责任链模式
![](https://user-images.githubusercontent.com/8423120/46203983-29be4180-c34e-11e8-8f21-ee3807490d3d.png)
责任链模式有三个角色：
- Handler（抽象处理者）：抽象类或者接口, 定义处理请求的方法以及持有下一个 Handler 的引用.
- ConcreteHandler1,ConcreteHandler2（具体处理者）：实现抽象处理类, 对请求进行处理, 如果不处理则转发给下一个处理者.
- Client (客户端): 即要使用责任链模式的地方。
例子：MotionEvent...

#### 观察者模式
![](https://user-images.githubusercontent.com/8423120/46203457-66893900-c34c-11e8-981e-7c43a4066b8e.png)
定义对象间的一种一个对多的依赖关系，当一个对象的状态发送改变时，所以依赖于它的对象都得到通知并被自动更新。
观察者模式有四个角色：
- Subject（抽象主题）：又叫抽象被观察者，把所有观察者对象的引用保存到一个集合里，每个主题都可以有任何数量的观察者。抽象主题提供一个接口，可以增加和删除观察者对象。
- ConcreteSubject（具体主题）：又叫具体被观察者，将有关状态存入具体观察者对象；在具体主题内部状态改变时，给所有登记过的观察者发出通知。
- Observer (抽象观察者): 为所有的具体观察者定义一个接口，在得到主题通知时更新自己。
- ConcrereObserver（具体观察者）：实现抽象观察者定义的更新接口，当得到主题更改通知时更新自身的状态。
例子：Adapter.notifySetChanged()、BroadcastReceiver...

#### 模板方法模式
![](https://user-images.githubusercontent.com/8423120/46202749-393b8b80-c34a-11e8-9d0c-db07316efd7f.png)
定义一个操作中的算法框架，而将一些步骤延迟到子类中，使得子类可以不改变一个算法的结构即可重定义算法的某些特定步骤。
模板方法模式有两个角色：
- AbstractClass（抽象类）：定义了一整套算法框架。
- ConcreteClass（具体实现类）：具体实现类，根据需要去实现抽象类中的方法。
例子：Activity.onCreate()、View.onDraw()...

#### 迭代器模式
![image](https://user-images.githubusercontent.com/8423120/46203990-32af1300-c34e-11e8-8645-d27c5d016750.png)
提供一种方法访问一个容器对象中各个元素，而又不需暴露该对象的内部细节。
迭代器模式有五个角色：
- Iterator（迭代器接口）：负责定义、访问和遍历元素的接口。
- ConcreteIterator（具体迭代器类）: 实现迭代器接口。
- Aggregate（容器接口）：定义容器的基本功能以及提供创建迭代器的接口。
- ConcreteAggregate（具体容器类）：实现容器接口中的功能。
- Client（客户端类）：即要使用迭代器模式的地方。
例子：Iterator、Cursor...

#### 备忘录模式
![image](https://user-images.githubusercontent.com/8423120/46204050-62f6b180-c34e-11e8-834a-a608d7a8614c.png)
在不破坏封装性的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态，这样以后就可以将该对象恢复到先前保存的状态。
备忘录模式有三个角色：
- Originator（发起人角色）：负责创建一个备忘录（Memoto），能够记录内部状态，以及恢复原来记录的状态。并且能够决定哪些状态是需要备忘的。
- Memoto（备忘录角色）：将发起人（Originator）对象的内部状态存储起来；并且可以防止发起人（Originator）之外的对象访问备忘录（Memoto）。
- Caretaker（负责人角色）：负责保存备忘录（Memoto），不能对备忘录（Memoto）的内容进行操作和访问，只能将备忘录传递给其他对象。
例子：Activity.onSaveInstanceState()...

#### 访问者模式
![](https://user-images.githubusercontent.com/8423120/46203513-9e907c00-c34c-11e8-8fd4-fe7e3b5dae08.png)
封装某些作用于某种数据结构中各元素的操作，它可以在不改变数据结构的前提下定义作用于这些元素的新的操作。
访问者模式有五个角色：
- Visitor（抽象访问者）：接口或者抽象类，为每一个元素（Element）声明一个访问的方法。
- ConcreteVisitor（具体访问者）：实现抽象访问者中的方法，即对每一个元素都有其具体的访问行为。
- Element（抽象元素）：接口或者抽象类，定义一个 accept 方法，能够接受访问者（Visitor）的访问。
- ConcreteElementA、ConcreteElementB（具体元素）：实现抽象元素中的 accept 方法，通常是调用访问者提供的访问该元素的方法。
- Client（客户端类）：即要使用访问者模式的地方。

#### 中介者模式
![](https://user-images.githubusercontent.com/8423120/46202892-a3543080-c34a-11e8-82bb-5c78ddfa3760.png)
也称为调解者模式或者调停者模式。当程序存在大量的类时，多个对象之间存在着依赖的关系，呈现出网状结构，那么程序的可读性和可维护性就变差了，并且修改一个类需要牵涉到其他类，不符合开闭原则。因此我们可以引入中介者，将网状结构转化成星型结构，可以降低程序的复杂性，并且可以减少各个对象之间的耦合。
中介者模式有四个角色：
- Mediator（抽象中介者）：抽象类或者接口, 定义统一的接口，用于各同事角色之间的通信。
- ConcreteMediator（具体中介者）：继承或者实现了抽象中介者，实现了父类定义的方法, 协调各个具体同事进行通信。
- Colleague（抽象同事）：抽象类或者接口, 定义统一的接口，它只知道中介者而不知道其他同事对象。
- ConcreteColleague（具体同事）：继承或者实现了抽象同事角色，每个具体同事类都知道自己本身的行为，其他的行为只能通过中介者去进行。
例子：KeyguardViewMediator...

#### 解释器模式
![image](https://user-images.githubusercontent.com/8423120/46204093-8ae61500-c34e-11e8-9801-d66a999a0363.png)
给定一门语言，定义它的文法的一种表示，并定义一个解释器，该解释器使用该表示来解释语言中的句子。
解释器模式有五个角色：
- AbstractExpression（抽象表达式）：定义一个抽象的解释方法，其具体的实现在各个具体的子类解释器中完成。
- TerminalExpression（终结符表达式）：实现对文法中与终结符有关的解释操作。
- NonterminalExpression（非终结符表达式）：实现对文法中的非终结符有关的解释操作。
- Context（环境角色）：包含解释器之外的全部信息。
- Client（客户端角色）：解析表达式，构建抽象语法树，执行具体的解释操作等。
例子：PackageParser...

#### 命令模式
![](https://user-images.githubusercontent.com/8423120/46203610-e9aa8f00-c34c-11e8-8cf2-6cce809d25d8.png)
将一个请求封装成一个对象，从而让你使用不同的请求把客户端参数化，对请求排队或者记录日志，可以提供命令的撤销和恢复功能。
命令模式有五个角色：
- Command（命令角色）：接口或者抽象类，定义要执行的命令。
- ConcreteCommand（具体命令角色）：命令角色的具体实现，通常会持有接收者，并调用接收者来处理命令。
- Invoker（调用者角色）：负责调用命令对象执行请求，通常会持有命令对象（可以持有多个命令对象）。Invoker 是 Client 真正触发命令并要求命令执行相应操作的地方（使用命令对象的入口）。
- Receiver（接收者角色）：是真正执行命令的对象。任何类都可能成为一个接收者，只要它能够实现命令要求实现的相应功能。
- Client（客户端角色）：Client 可以创建具体的命令对象，并且设置命令对象的接收者。
例子：Handler...



[Home](../../README.md)