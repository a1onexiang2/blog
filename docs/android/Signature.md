[Home](../../README.md)  

# Android  

## Signature  

#### v1 & v2  
Android 7.0（Nougat）引入一项新的应用签名方案 APK Signature Scheme v2，它是一个对全文件进行签名的方案，能提供更快的应用安装时间、对未授权 APK 文件的更改提供更多保护，在默认情况下，Android Gradle 2.2.0 插件会使用 APK Signature Scheme v2 和传统签名方案来签署你的应用。  
新的签名方案会在 ZIP 文件格式的 Central Directory 区块所在文件位置的前面添加一个 APK Signing Block 区块，下面按照 ZIP 文件的格式来分析新应用签名方案签名后的 APK 包。  
整个 APK（ZIP 文件格式）会被分为以下四个区块： 
1. Contents of ZIP entries（from offset 0 until the start of APK Signing Block）
2. APK Signing Block
3. ZIP Central Directory
4. ZIP End of Central Directory
![image](https://user-images.githubusercontent.com/8423120/47836325-d45cd080-dde2-11e8-8c14-605cae1c8502.png)

新应用签名方案的签名信息会被保存在区块 2（APK Signing Block）中， 而区块 1（Contents of ZIP entries）、区块 3（ZIP Central Directory）、区块 4（ZIP End of Central Directory）是受保护的，在签名后任何对区块 1、3、4 的修改都逃不过新的应用签名方案的检查。

#### v2 模式下渠道包方案
区块 2（APK Signing Block）结构如下：
偏移 | 字节数 | 描述
-- | -- | --
@+0 | 8 | 这个 Block 的长度（本字段的长度不计算在内）
@+8 | n | 一组 ID-value
@-24 | 8 | 这个 Block 的长度（和第一个字段一样值）
@-16 | 16 | 魔数 “APK Sig Block 42”

源代码中是通过查找 ID 为 APK_SIGNATURE_SCHEME_V2_BLOCK_ID == 0x7109871a 的 ID-value，来获取 APK Signature Scheme v2 Block，而对这个区块中其他的 ID-value 选择了忽略。可以提供一个自定义的 ID-value 并写入该区域，从而快速生成渠道包。所以可以采用以下步骤来实现快速多渠道打包方案：
1. 对新的应用签名方案生成的 APK 包中的 ID-value 进行扩展，提供自定义 ID－value（渠道信息），并保存在 APK 中；
2. 而 APK 在安装过程中进行的签名校验，是忽略我们添加的这个 ID-value 的，这样就能正常安装了；
3. 在 App 运行阶段，可以通过 ZIP 的 EOCD（End of central directory）、Central directory 等结构中的信息找到添加的 ID-value，从而实现获取渠道信息的功能。


[Home](../../README.md)  