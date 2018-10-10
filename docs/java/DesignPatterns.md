[Home](../../README)

# Java

## Design Patterns
设计模式有六大原则：
1. 单一职责原则（Single Responsibility Principle，SRP）
一个类应只包含单一的职责。
一个类职责过大的话，首先引起的问题就是这个类过于臃肿，其复用性比较差，并且如果修改某个职责，有可能引起另一个职责发生错误。
2. 开放封闭原则 (Open - Closed Principle，OCP)
一个模块、类、函数应当是对修改关闭，扩展开放。
修改原有的代码可能会导致原本正常的功能出现问题。因此，当需求有变化时最好是通过扩展来实现，增加新的方法满足需求，而不是去修改原有代码。
3. 里氏代换原则 (Liskov Substitution Principle，LSP)
使用父类的地方能够使用子类来替换，反过来则不行。
在程序中尽量使用基类类型来对对象进行定义，而在运行时再确定其子类类型，用子类对象来替换父类对象。子类的所有方法必须在父类中声明，或子类必须实现父类中声明的所有方法。
4. 依赖倒转原则 (Dependence Inversion Principle，DIP)
抽象不应该依赖于细节，细节应当依赖于抽象。
即要面向接口编程，而不是面向具体实现去编程。高层模块不应该依赖低层模块，应该去依赖抽象。
5. 接口隔离法则 (Interface Segregation Principle，ISL）
一个类对另一个类的依赖应该建立在最小的接口上。
一个类不应该依赖他不需要的接口。接口的粒度要尽可能小，如果一个接口的方法过多，可以拆成多个接口。
6. 迪米特法则 (Law of Demeter，LoD)
一个类尽量不要与其他类发生关系。
一个类对其他类知道的越少，耦合越小。当修改一个类时，其他类的影响就越小，发生错误的可能性就越小。

设计模式一般分为三类：创建型模式、结构型模式、行为型模式。

### 创建型模式

#### 单例模式
确保某个类只有一个实例, 并且自行实例化并向整个系统提供这个实例。
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

#### 建造者模式
![](https://user-images.githubusercontent.com/8423120/46240270-52d6e480-c3d7-11e8-9d12-3531ae42f4bc.png)
将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。
建造者模式有四个角色：
- Product（产品类）：要创建的复杂对象。在本类图中，产品类是一个具体的类，而非抽象类。实际编程中，产品类可以是由一个抽象类与它的不同实现组成，也可以是由多个抽象类与他们的实现组成。
- Builder（抽象建造者）：创建产品的抽象接口，一般至少有一个创建产品的抽象方法和一个返回产品的抽象方法。引入抽象类，是为了更容易扩展。
- ConcreteBuilder（实际的建造者）：继承 Builder 类，实现抽象类的所有抽象方法。实现具体的建造过程和细节。
- Director（指挥者类）：分配不同的建造者来创建产品，统一组装流程。
例子：AlertDialog.Builder、Notification.Builder...

#### 工厂方法模式
![](https://user-images.githubusercontent.com/8423120/46240274-6bdf9580-c3d7-11e8-946e-08b274eb4f0c.png)
定义一个用于创建对象的接口，让子类决定实例化哪个类。生成复杂对象时，无需知道具体类名，只需知道相应的工厂方法即可。
工厂方法模式有四个角色：
- Product（抽象产品类）：要创建的复杂对象，定义对象的公共接口。
- ConcreteProduct（具体产品类）：实现 Product 接口。
- Factory（抽象工厂类）：该方法返回一个 Product 类型的对象。
- ConcreteFactory（具体工厂类）：返回 ConcreteProduct 实例。
例子：ThreadFactory...

#### 简单工厂模式
![](https://user-images.githubusercontent.com/8423120/46240290-bcef8980-c3d7-11e8-8af9-a6b9e1fce304.png)
定义一个用于创建对象的接口，让子类决定实例化哪个类。工厂方法模式有抽象工厂类，简单工厂模式没有抽象工厂类且其工厂类的工厂方法是静态的。
简单工厂模式有三个角色：
- Product（抽象产品类）：要创建的复杂对象，定义对象的公共接口。
- ConcreteProduct（具体产品类）：实现 Product 接口。
- Factory（工厂类）：返回 ConcreteProduct 实例。

#### 抽象工厂模式
![](https://user-images.githubusercontent.com/8423120/46240295-cd9fff80-c3d7-11e8-8c93-5d2aadb0b07b.png)
为创建一组相关或者相互依赖的对象提供一个接口，而无需指定它们的具体类。抽象工厂模式则可以提供多个产品对象，而不是单一的产品对象。
抽象工厂模式有四个角色：
- AbstractProduct（抽象产品类）：定义产品的公共接口。
- ConcreteProduct（具体产品类）：定义产品的具体对象，实现抽象产品类中的接口。
- AbstractFactory（抽象工厂类）：定义工厂中用来创建不同产品的方法。
- ConcreteFactory（具体工厂类）：实现抽象工厂中定义的创建产品的方。

#### 原型模式
![](https://user-images.githubusercontent.com/8423120/46240308-122b9b00-c3d8-11e8-8f82-b34b9d1376dc.png)
用原型实例指定创建对象的种类，并通过拷贝这些原型创建新的对象。
原型模式有三个角色：
- Prototype（抽象原型类）：抽象类或者接口，用来声明 clone 方法。
- ConcretePrototype1、ConcretePrototype2（具体原型类）：即要被复制的对象。
- Client（客户端类）：即要使用原型模式的地方。
例子：Bundle、Intent...

### 行为型模式

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
![](https://user-images.githubusercontent.com/8423120/46240333-60d93500-c3d8-11e8-89b8-bbc38e5748f4.png)
定义对象间的一种一个对多的依赖关系，当一个对象的状态发送改变时，所以依赖于它的对象都得到通知并被自动更新。
观察者模式有四个角色：
- Subject（抽象主题）：又叫抽象被观察者，把所有观察者对象的引用保存到一个集合里，每个主题都可以有任何数量的观察者。抽象主题提供一个接口，可以增加和删除观察者对象。
- ConcreteSubject（具体主题）：又叫具体被观察者，将有关状态存入具体观察者对象；在具体主题内部状态改变时，给所有登记过的观察者发出通知。
- Observer (抽象观察者): 为所有的具体观察者定义一个接口，在得到主题通知时更新自己。
- ConcrereObserver（具体观察者）：实现抽象观察者定义的更新接口，当得到主题更改通知时更新自身的状态。
例子：Adapter.notifySetChanged()、BroadcastReceiver...

#### 模板方法模式
![](https://user-images.githubusercontent.com/8423120/46240337-72bad800-c3d8-11e8-9fa3-d2143b5e2da6.png)
定义一个操作中的算法框架，而将一些步骤延迟到子类中，使得子类可以不改变一个算法的结构即可重定义算法的某些特定步骤。
模板方法模式有两个角色：
- AbstractClass（抽象类）：定义了一整套算法框架。
- ConcreteClass（具体实现类）：具体实现类，根据需要去实现抽象类中的方法。
例子：Activity.onCreate()、View.onDraw()...

#### 迭代器模式
![](https://user-images.githubusercontent.com/8423120/46203990-32af1300-c34e-11e8-8645-d27c5d016750.png)
提供一种方法访问一个容器对象中各个元素，而又不需暴露该对象的内部细节。
迭代器模式有五个角色：
- Iterator（迭代器接口）：负责定义、访问和遍历元素的接口。
- ConcreteIterator（具体迭代器类）: 实现迭代器接口。
- Aggregate（容器接口）：定义容器的基本功能以及提供创建迭代器的接口。
- ConcreteAggregate（具体容器类）：实现容器接口中的功能。
- Client（客户端类）：即要使用迭代器模式的地方。
例子：Iterator、Cursor...

#### 备忘录模式
![](https://user-images.githubusercontent.com/8423120/46204050-62f6b180-c34e-11e8-834a-a608d7a8614c.png)
在不破坏封装性的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态，这样以后就可以将该对象恢复到先前保存的状态。
备忘录模式有三个角色：
- Originator（发起人角色）：负责创建一个备忘录（Memoto），能够记录内部状态，以及恢复原来记录的状态。并且能够决定哪些状态是需要备忘的。
- Memoto（备忘录角色）：将发起人（Originator）对象的内部状态存储起来；并且可以防止发起人（Originator）之外的对象访问备忘录（Memoto）。
- Caretaker（负责人角色）：负责保存备忘录（Memoto），不能对备忘录（Memoto）的内容进行操作和访问，只能将备忘录传递给其他对象。
例子：Activity.onSaveInstanceState()...

#### 访问者模式
![](https://user-images.githubusercontent.com/8423120/46240342-8108f400-c3d8-11e8-8a37-fdb55e1df426.png)
封装某些作用于某种数据结构中各元素的操作，它可以在不改变数据结构的前提下定义作用于这些元素的新的操作。
访问者模式有五个角色：
- Visitor（抽象访问者）：接口或者抽象类，为每一个元素（Element）声明一个访问的方法。
- ConcreteVisitor（具体访问者）：实现抽象访问者中的方法，即对每一个元素都有其具体的访问行为。
- Element（抽象元素）：接口或者抽象类，定义一个 accept 方法，能够接受访问者（Visitor）的访问。
- ConcreteElementA、ConcreteElementB（具体元素）：实现抽象元素中的 accept 方法，通常是调用访问者提供的访问该元素的方法。
- Client（客户端类）：即要使用访问者模式的地方。

#### 中介者模式
![](https://user-images.githubusercontent.com/8423120/46240347-90883d00-c3d8-11e8-933b-8c387e573705.png)
用一个中介者对象来封装一系列的对象交互。中介者使得各对象不需要显式地相互引用，从而使其松散耦合，而且可以独立地改变它们之间的交互。
中介者模式有四个角色：
- Mediator（抽象中介者）：抽象类或者接口, 定义统一的接口，用于各同事角色之间的通信。
- ConcreteMediator（具体中介者）：继承或者实现了抽象中介者，实现了父类定义的方法, 协调各个具体同事进行通信。
- Colleague（抽象同事）：抽象类或者接口, 定义统一的接口，它只知道中介者而不知道其他同事对象。
- ConcreteColleague（具体同事）：继承或者实现了抽象同事角色，每个具体同事类都知道自己本身的行为，其他的行为只能通过中介者去进行。
例子：KeyguardViewMediator...

#### 解释器模式
![](https://user-images.githubusercontent.com/8423120/46204093-8ae61500-c34e-11e8-9801-d66a999a0363.png)
给定一门语言，定义它的文法的一种表示，并定义一个解释器，该解释器使用该表示来解释语言中的句子。
解释器模式有五个角色：
- AbstractExpression（抽象表达式）：定义一个抽象的解释方法，其具体的实现在各个具体的子类解释器中完成。
- TerminalExpression（终结符表达式）：实现对文法中与终结符有关的解释操作。
- NonterminalExpression（非终结符表达式）：实现对文法中的非终结符有关的解释操作。
- Context（环境角色）：包含解释器之外的全部信息。
- Client（客户端角色）：解析表达式，构建抽象语法树，执行具体的解释操作等。
例子：PackageParser...

#### 命令模式
![](https://user-images.githubusercontent.com/8423120/46240351-a0a01c80-c3d8-11e8-8899-68db0f27a337.png)
将一个请求封装成一个对象，从而让你使用不同的请求把客户端参数化，对请求排队或者记录日志，可以提供命令的撤销和恢复功能。
命令模式有五个角色：
- Command（命令角色）：接口或者抽象类，定义要执行的命令。
- ConcreteCommand（具体命令角色）：命令角色的具体实现，通常会持有接收者，并调用接收者来处理命令。
- Invoker（调用者角色）：负责调用命令对象执行请求，通常会持有命令对象（可以持有多个命令对象）。Invoker 是 Client 真正触发命令并要求命令执行相应操作的地方（使用命令对象的入口）。
- Receiver（接收者角色）：是真正执行命令的对象。任何类都可能成为一个接收者，只要它能够实现命令要求实现的相应功能。
- Client（客户端角色）：Client 可以创建具体的命令对象，并且设置命令对象的接收者。
例子：Thread、Handler...

### 结构型模式

#### 代理模式
![](https://user-images.githubusercontent.com/8423120/46239540-4dc06800-c3cc-11e8-9f72-c40bcf548a1a.png)
为其他对象提供一种代理以控制这个对象的访问。
代理模式有四个角色：
- Subject（抽象主题类）：接口或者抽象类，声明真实主题与代理的共同接口方法。
- RealSubject（真实主题类）：也叫做被代理类或被委托类，定义了代理所表示的真实对象，负责具体业务逻辑的执行，客户端可以通过代理类间接的调用真实主题类的方法。
- Proxy（代理类）：也叫委托类，持有对真实主题类的引用，在其所实现的接口方法中调用真实主题类中相应的接口方法执行。
- Client（客户端类）：使用代理模式的地方。
例子：ActivityManagerProxy...

#### 动态代理
代理模式分为静态代理与动态代理。代理类在程序运行前不存在、运行时由程序动态生成的代理方式称为动态代理。实现动态代理包括三步：
1. 新建委托类。
2. 实现 InvocationHandler 接口，这是负责连接代理类和委托类的中间类必须实现的接口。
3. 通过 Proxy 类的静态方法新建代理类对象。

#### 组合模式
![](https://user-images.githubusercontent.com/8423120/46239574-c3c4cf00-c3cc-11e8-9497-39646706de3b.png)
将对象组合成树形结构以表示 “部分 - 整体” 的层次结构，使得用户对单个对象和组合对象的使用具有一致性。
组合模式有三个角色：
- Component（抽象组件角色）：定义参加组合对象的共有方法和属性，可以定义一些默认的函数或属性。
- Leaf（叶子结点）：叶子没有子结点，因此是组合树的最小构建单元。
- Composite（树枝结点）：定义所有枝干结点的行为，存储子结点，实现相关操作。
例子：ViewGroup、View...

#### 适配器模式
![](https://user-images.githubusercontent.com/8423120/46239608-29b15680-c3cd-11e8-9469-193db0436dcd.png)
将一个类的接口变换成客户端所期待的另一种接口，从而使原本因接口不匹配而无法在一起工作的两个类能够在一起工作。
适配器模式有三个角色：
- Adapter（适配器接口）：即目标角色，定义把其他类转换为何种接口，也就是我们期望的接口。
- Adaptee（被适配角色）：即源角色，一般是已存在的类，需要适配新的接口。
- ConcreteAdapter（具体适配器）：实现适配器接口，把源角色接口转换为目标角色期望的接口。
例子：Adapter...

#### 装饰者模式
![](https://user-images.githubusercontent.com/8423120/46239629-5c5b4f00-c3cd-11e8-8c5a-7e247444008d.png)
动态地给一个对象添加一些额外的职责。就增加功能来说，装饰模式相比生成子类更为灵活。
装饰者模式有四个角色：
- Component（抽象组件）：接口或者抽象类，被装饰的最原始的对象。具体组件与抽象装饰角色的父类。
- ConcreteComponent（具体组件）：实现抽象组件的接口。
- Decorator（抽象装饰角色）：一般是抽象类，抽象组件的子类，同时持有一个被装饰者的引用，用来调用被装饰者的方法; 同时可以给被装饰者增加新的职责。
- ConcreteDecorator（具体装饰类）：抽象装饰角色的具体实现。
例子：ContextWrapper...

#### 享元模式
![](https://user-images.githubusercontent.com/8423120/46239722-fec80200-c3ce-11e8-93dd-ce15439158eb.png)
有效地支持大量的细粒度的对象。
享元模式有四个角色：
- Flyweight（抽象享元角色）：接口或抽象类，可以同时定义出对象的外部状态和内部状态的接口或实现。
- ConcreteFlyweight（具体享元角色）：实现抽象享元角色中定义的业务。
- UnsharedConcreteFlyweight（不可共享的享元角色）：并不是所有的抽象享元类的子类都需要被共享，不能被共享的子类可设计为非共享具体享元类；当需要一个非共享具体享元类的对象时可以直接通过实例化创建。该对象一般不会出现在享元工厂中。
- FlyweightFactory（享元工厂）：管理对象池和创建享元对象。
例子：String...

#### 外观模式
![](https://user-images.githubusercontent.com/8423120/46239697-9bd66b00-c3ce-11e8-8102-2db3a7118b11.png)
要求一个子系统的外部与其内部的通信必须通过一个统一的对象进行。外观模式提供一个高层次的接口，使得子系统更易于使用。
外观模式有两个角色：
- Facade（外观角色）：对外的统一入口。
- Complex System（复杂系统）：一般由多个子系统构成，负责具体功能的实现。
例子：Context...

#### 桥接模式
![](https://user-images.githubusercontent.com/8423120/46239717-f4a60380-c3ce-11e8-9352-0102ebb4e11b.png)
将抽象部分与实现部分分离，使它们都可以独立的变化。
桥接模式有三个角色：
- Abstraction（抽象化角色）：一般是抽象类，定义该角色的行为，同时保存一个对实现化角色的引用。
- Implementor（实现化角色）：接口或者抽象类，定义角色必需的行为和属性。
- ConcreteImplementorA、ConcreteImplementorB（具体实现化角色）：实现角色的具体行为。
例子：AbsListView & ListAdapter...

[Home](../../README)