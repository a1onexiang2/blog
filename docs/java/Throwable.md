[Home](../../README.md)

# Java

## Throwable

Throwable 类是 Java 语言中所有错误或异常的超类。它的两个子类是 Error 和 Exception。
所有实现 Throwable 的类都可以在方法体中作为 throws 的对象。也可以在 try/catch 中捕获。

#### Error
Error 类包括一些严重的程序不能处理的系统错误类，如内存溢出、虚拟机错误、栈溢出等。
这类错误一般与虚拟机相关，对于这类错误的导致的应用程序中断，仅靠程序本身无法恢复和预防，遇到这样的错误，建议让程序终止。
- **OutOfMemoryError**
内存溢出，一般发生在内存不足的情况下。
- **StackOverflowError**
栈溢出，一般发生在无限循环或无限递归的情况下。

#### Exception
Exception 类及其子类是 Throwable 的一种形式，它指出了合理的应用程序想要捕获的条件。
有些异常在编写程序时无法预料的，如中断异常、非法存取异常等。为了保证程序的健壮性，Java 要求必须对这些可能出现的异常进行捕获，并对其进行处理。

#### Checked Exception、Runtime Exception
Java 包含两种 Exception：Checked Exception 和 Runtime Exception。两者之间的区别是：
Checked Exception 必须被显式地捕获或者传递，可以 throws 抛出给上层处理，或者通过 try/catch/finally 捕获。而 Runtime Exception 则可不必抛出或捕获。
Checked Exception 继承自 `java.lang.Exception` 类，Runtime Exception 继承自 `java.lang.RuntimeException` 类。

[Home](../../README.md)