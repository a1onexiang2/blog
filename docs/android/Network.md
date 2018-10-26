[Home](../../README.md)  

# Android  

## Network  

#### OkHttp  
![image](https://user-images.githubusercontent.com/8423120/46715758-f3f55300-cc93-11e8-87df-424ce0ce5861.png)  
- **Call**  
OkHttpClient 实现了 Call.Factory，负责根据请求创建新的 Call（RealCall）类。发起一个请求会经历以下步骤：  
    1. 在每次执行前，会先检查这个 Call 是否已经被执行了，每个 Call 只能被执行一次，如果需要一个一样的 Call，可以调用 `Call.clone()` 方法克隆。  
    2. 检查通过之后会调用 `client.dispatcher().execute(this)` 来进行实际执行，dispatcher 是 OkHttpClient.Builder 的成员之一，它是 HTTP 请求的执行策略。  
    3. 调用 `getResponseWithInterceptorChain()` 函数获取 HTTP 返回结果。  
    4. 通知 dispatcher 自己已经执行完毕。  
- **Interceptor**  
Interceptor 是 OkHttp 最核心的内容，它把实际的网络请求、缓存、透明压缩等功能都统一了起来，每一个功能都只是一个 Interceptor，再把它们连接成一个 Interceptor.Chain，环环相扣，最终圆满完成一次网络请求。  
Interceptor 分别为：  
    1. 配置 OkHttpClient 时设置的 interceptors。  
    2. 负责失败重试以及重定向的 RetryAndFollowUpInterceptor。  
    3. 负责把用户构造的请求转换为发送到服务器的请求、把服务器返回的响应转换为用户友好的响应的 BridgeInterceptor。  
    4. 负责读取缓存直接返回、更新缓存的 CacheInterceptor。  
    5. 负责和服务器建立连接的 ConnectInterceptor。  
    6. 配置 OkHttpClient 时设置的 networkInterceptors。  
    7. 负责向服务器发送请求数据、从服务器读取响应数据的 CallServerInterceptor。  

Interceptor 的顺序决定了功能，最后一个 Interceptor 一定是负责和服务器实际通讯的，重定向、缓存等一定是在实际通讯之前的。  
- **Response**  
每个 ResponseBody 只能被消费一次，多次消费会抛出异常。并且 ResponseBody 必须被关闭，否则会发生资源泄漏。  
- **Cache**  
具体的缓存逻辑 OkHttp 内置封装了一个 Cache 类，它利用 DiskLruCache，用磁盘上的有限大小空间进行缓存，按照 LRU 算法进行缓存淘汰。  
可以在 OkHttpClient.Builder 设置 Cache 时调用 `Cache(File directory, long maxSize)` 指定目录和缓存大小。也可以自行实现 InternalCache 接口使用自定义的缓存策略。  

#### HttpURLConnection、HttpsURLConnection  
在 Android 4.4 之后的 HttpUrlConnection 的实现基于 OkHttp，具体的方法则是通过 HttpHandler 作为桥梁，在 OkHttpClient、HttpEngine 中增加相应的方法来实现。  
HttpURLConnection、HttpsURLConnection 基于的 OkHttp 版本没有跟上主流版本，使用它们进行网络请求是有风险的。  

#### Retrofit  
Retrofit 使用了[**动态代理**](../java/DesignPatterns.md#动态代理)技术。主要的结构如下：  
![image](https://user-images.githubusercontent.com/8423120/46720246-961c3780-cca2-11e8-80eb-a122319facd4.png)  
一个完整的网络请求执行流程如下：  
1. 通过 `Retrofit.Builder.build()` 方法来构建一个实例。  
2. 通过动态代理和代码生成，把接口的方法转成一次 HTTP 调用时，以下四个成员很重要：  
    - **callFactory**  
    callFactory 负责创建 HTTP 请求，HTTP 请求被抽象为了 okhttp3.Call 类，它表示一个已经准备好，可以随时执行的 HTTP 请求。在构造 Retrofit 对象时，可以指定 callFactory，如果不指定，将默认设置为一个 okhttp3.OkHttpClient。  
    - **callAdapter**  
    callAdapter 把 retrofit2.Call<T> 转为 T（注意和 okhttp3.Call 区分，retrofit2.Call<T> 表示的是对一个 Retrofit 方法的调用），这个过程会发送一个 HTTP 请求，拿到服务器返回的数据（通过 okhttp3.Call 实现），并把数据转换为声明的 T 类型对象（通过 Converter<F, T> 实现）。  
    - **responseConverter**  
    responseConverter 是 Converter<ResponseBody, T> 类型，负责把服务器返回的数据（JSON、XML、二进制或者其他格式，由 ResponseBody 封装）转化为 T 类型的对象。  
    - **parameterHandlers**  
    parameterHandlers 则负责解析 API 定义时每个方法的参数，并在构造 HTTP 请求时设置参数。  
3. 生成一个 OkHttpCall 并调用 `serviceMethod.callAdapter.adapt(OkHttpCall)`。  
4. 对结果进行解析。调用 `serviceMethod.toResponse(catchingBody)` 并根据设置的 ConverterFactory 解析返回的数据，生成一个 Response 实例。  
5. 回调 ExecutorCallAdapterFactory 中，`CallAdapterFactory.adapt()` 会生成 ExecutorCallbackCall 对象，调用 `ExecutorCallbackCall,enqueue(CallBack)` 方法，会调用 `MainThreadExecutor.execute()` 方法利用 handler 切换到主线程。  
6. 对返回的 Call 对象进行了进一步封装，生成 Observable<Response>。  


[Home](../../README.md)  