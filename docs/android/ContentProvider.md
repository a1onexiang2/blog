[Home](../../README.md)  

# Android  

## ContentProvider  

#### ContentProvider 是什么？解决了什么问题？有什么使用场景？  

ContentProvider 可以理解为一个 Android App 对外开放的接口。  
只要是符合它所定义的 Uri 格式的请求，均可以正常访问执行操作，即增、删、改、查。  
其他的 Android App 可以使用 ContentResolver 对象通过与 ContentProvider 同名的方法请求执行，被执行的就是 ContentProvider 中的同名方法。  
采用 ContentProvider 方式，ContentProvider 底层采用了 Binder 机制。  
解耦了底层数据的存储方式，使得无论底层数据存储采用何种方式，外界对数据的访问方式都是统一的。  
ContentProvider 使数据访问更安全、简单、高效。  
Android 7.0 以上安装 apk 文件使用了 FileProvider，在获取照相机拍摄图片时，在选择相册图片时等等。  

[Home](../../README.md)  