[Home](../../README.md)

# Standard

## Https
HTTPS 由两部分组成：HTTP、SSL/TLS，即在 HTTP 上又加了一层处理加密信息的模块。使用 HTTPS 协议之后在网络上传输的数据是加密的密文，即便被拦截，没有密钥进行解密的话也就是一串乱码。端口号是 443。

#### Http
HTTP 构建于 TCP/IP 协议之上，默认端口号是 80，HTTP 协议有如下特点：
1. 简单快速
   客户向服务器请求服务时，只需传送请求方法和路径。请求方法常用的有 GET、HEAD、POST。每种方法规定了客户与服务器联系的类型不同。由于 HTTP 协议简单，使得 HTTP 服务器的程序规模小，因而通信速度很快。
2. 灵活
   HTTP 允许传输任意类型的数据对象。正在传输的类型由 Content-Type 加以标记。
3. 无连接
   无连接的含义是限制每次连接只处理一个请求。服务器处理完客户的请求，并收到客户的应答后，即断开连接。采用这种方式可以节省传输时间。
4. 无状态
   HTTP 协议是无状态协议。无状态是指协议对于事务处理没有记忆能力。缺少状态意味着如果后续处理需要前面的信息，则它必须重传，这样可能导致每次连接传送的数据量增大。另一方面，在服务器不需要先前信息时它的应答就较快。
5. 支持 B/S 及 C/S 模式

#### URL
HTTP 使用统一资源标识符（Uniform Resource Identifiers，URI）来传输数据和建立连接。
URL，全称是 UniformResourceLocator，中文叫统一资源定位符，是互联网上用来标识某一处资源的地址。URL 是一种特殊类型的 URI，包含了用于查找某个资源的足够的信息。
```
protocol://host[:port]/[path][?query][#fragment]
```
一个完整的 URL，主要包含七个部分：
1. 协议部分（Protocol）
   在 Internet 中可以使用多种协议，如 HTTP、FTP 等等。后面的 `//` 为分隔符。
2. 域名部分（Host）
   URL 的具体域名，也可以使用 IP 地址作为域名使用。
3. 端口部分（Port）
   跟在域名后面，域名和端口之间使用 `:` 作为分隔符。端口不是一个 URL 必须的部分，如果省略端口部分，将采用默认端口。
4. 路径部分（Path）
   从域名后的第一个 `/` 开始到最后一个 `/` 为止，是路径部分。路径部分不是 URL 必须的部分。
5. 查询部分（Query）
   从 `?` 开始到 `#` 为止之间的部分为查询部分，又称搜索部分。参数可以允许有多个参数，参数与参数之间用 `&` 作为分隔符。查询部分不是 URL 必须的部分。
6. 锚部分（Fragment）
   从 `#` 开始到最后，都是锚部分。锚部分不是 URL 必须的部分。

#### Request（请求信息）
发出的请求信息包括以下内容：
```
// 请求行
GET /path1/file HTTP/1.1
// 请求头
Host: host:port
User-Agent: Mozilla/5.0 (Windows NT 6.1;WOW64; rv:37.0) Gecko/20100101 Firefox/37.0
Accept:text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language:en-GB,en-US;q=0.8,zh-CN;q=0.6,zh;q=0.4,en;q=0.2
Accept-Encoding: gzip, deflate
Connection: keep-alive
// 一个空行

// 实体内容
key1=value1&key2=value2
```
![image](https://user-images.githubusercontent.com/8423120/46244331-efb57400-c40f-11e8-8f0f-37def2703329.png)

#### Response（响应信息）
获得的响应信息包括以下内容：
```
// 状态行
HTTP/1.1 200 OK
// 响应头
Server:Apache Tomcat/5.0.12
Date:Mon,6Oct2003 13:23:42 GMT
Content-Length:112
// 一个空行

// 响应正文
content...
```

#### Http 请求方法
![image](https://user-images.githubusercontent.com/8423120/46252516-26d16700-c49c-11e8-98d2-605c6654998e.png)

#### Keep-Alive
我们知道 HTTP 协议采用 “请求 - 应答” 模式，当使用普通模式，即非 Keep-Alive 模式时，每个请求/应答客户和服务器都要新建一个连接，完成之后立即断开连接（HTTP 协议为无连接的协议）；当使用 Keep-Alive 模式（又称持久连接、连接重用）时，Keep-Alive 功能使客户端到服务器端的连接持续有效，当出现对服务器的后继请求时，Keep-Alive 功能避免了建立或者重新建立连接。
在 HTTP/1.1 版本中，默认情况下所有连接都被保持，如果加入 "Connection: close" 才关闭。目前大部分浏览器都使用 HTTP/1.1 协议，也就是说默认都会发起 Keep-Alive 的连接请求了，所以是否能完成一个完整的 Keep-Alive 连接就看服务器设置情况。
HTTP Keep-Alive 简单说就是保持当前的 TCP 连接，避免了重新建立连接。
HTTP 长连接不可能一直保持，例如 Keep-Alive: timeout=5, max=100，表示这个 TCP 通道可以保持 5 秒，max=100，表示这个长连接最多接收 100 次请求就断开。
HTTP 是一个无状态协议，这意味着每个请求都是独立的，Keep-Alive 没能改变这个结果。另外，Keep-Alive 也不能保证客户端和服务器之间的连接一定是活跃的，在 HTTP1.1 版本中也如此。唯一能保证的就是当连接被关闭时你能得到一个通知，所以不应该让程序依赖于 Keep-Alive 的保持连接特性，否则会有意想不到的后果。

#### 分片传输 & 断点续传
在请求资源时，如果服务器响应头中有 `accept-ranges=bytes`，表明服务器支持 bytes 类型得数据断点续传。如果为空或者 none，可能不支持断点续传。
HTTP/1.1 协议（RFC2616）中定义了 HTTP 头 Range 和 Content-Range 字段。Range 用于指定资源的 byte 范围。
响应信息如果返回状态码 206，表明已经处理了部分请求，对于多重范围的范围请求，响应会在首部字段 Content-Type 中标明 multipart/byteranges。
终端在发起续传请求时应该在 HTTP 头中申明 If-Match 或者 If-Modified-Since 字段，帮助服务端判别文件变化。
另外 RFC2616 中同时定义有一个 If-Range 头，终端如果在续传时使用 If-Range。If-Range 中的内容可以为最初收到的 ETag 头或者是 Last-Modfied 中的最后修改时候。服务端在收到续传请求时，通过 If-Range 中的内容进行校验，校验一致时返回 206 的续传回应，不一致时服务端则返回 200 回应，回应的内容为新的文件的全部数据。

#### SSL/TLS
SSL 和 TLS 是不同时期的协议叫法，一般都叫 SSL 证书。TLS 是在 SSL 基础上发展的新版本，有时候会统称该协议为 SSL。
SSL 为了解决三大风险设计：
1. 窃听风险（eavesdropping）
   所有信息都是加密传播，第三方无法窃听。
2. 篡改风险（tampering）
   具有校验机制，一旦被篡改，通信双方会立刻发现。
3. 冒充风险（pretending）
   配备身份证书，防止身份被冒充。

#### Https 通讯流程
HTTPS 要使客户端与服务器端的通信过程得到安全保证，必须使用对称加密算法，但是协商对称加密算法的过程，需要使用非对称加密算法来保证安全，然而直接使用非对称加密的过程本身也不安全，会有中间人篡改公钥的可能性，所以客户端与服务器不直接使用公钥，而是使用数字证书签发机构（CA）颁发的证书来保证非对称加密过程本身的安全。这样通过这些机制协商出一个对称加密算法，就此双方使用该算法进行加密解密。从而解决了客户端与服务器端之间的通信安全问题。一次 Https 通讯的大致流程如下：
![image](https://user-images.githubusercontent.com/8423120/46243773-f93bdd80-c409-11e8-8a2b-6f863a5a24f3.png)
1. 客户端通过发送 Client Hello 报文开始 SSL 通信。报文中包含客户端支持的 SSL 的指定版本、加密组件（Cipher Suite）列表（所使用的加密算法及密钥长度等）。**注意：客户端还会附加一个随机数，这里记为 A。**
2. 服务器可进行 SSL 通信时，会以 Server Hello 报文作为应答。和客户端一样，在报文中包含 SSL 版本以及加密组件。服务器的加密组件内容是从接收到的客户端加密组件内筛选出来的。**注意：这里服务器同样会附加一个随机数，发给客户端，这里记为 B。**
3. 接着服务端将自己的公钥等信息发给 CA 申请证书并数字签名，CA 用自己的私钥进行加密，然后将其都发还给服务器，服务器再将其发给客户端。
4. 客户端尝试用自己系统内置的所有 CA 的公钥对服务端发过来的公钥进行解密，如果所有 CA 的公钥都无法解密的话，说明服务端发过来的证书是不被信任的，弹出警告；如果用自己内置的某个 CA 公钥对服务端发过来的公钥进行解密成功，说明服务端发过来的证书是正常的，然后对服务端的证书进行有效期、机构信息等进行验证，如果验证不通过 (如：过期)，则弹出警告。
5. 接着，客户端以 Client Key Exchange 报文作为回应。报文中包含通信加密中使用随机生成的 Pre-master secret 的随机密码串。该报文使用从证书中解密获得的公钥进行加密（其实就是服务器的公钥）。当其生成了 Pre-master secret 之后，会结合原来的 A、B，用 DH 算法计算出一个 master secret，紧接着根据这个 master secret 推导出 hash secret 和 session secret。
6. 客户端继续发送 Change Cipher Spec 报文。用于告知服务端，客户端已经切换到之前协商好的加密套件（Cipher Suite）的状态，准备使用之前协商好的加密套件加密数据并传输了。发送 finish 报文。
7. 服务器接收到客户端的请求之后，使用私钥解密报文，把 Pre-master secret 取出来。接着，服务器同样发送 Change Cipher Spec 报文和 finish 报文。当服务器解密获得了 Pre-master secret 之后，会结合原来的 A、B，用 DH 算法计算出一个 master secret，紧接着根据这个 master secret 推导出 hash secret 和 session secret。
8. 服务器和客户端的 Finished 报文交换完毕之后，SSL 连接就算建立完成。当然，通信会受到 SSL 的保护。从此处开始进行应用层协议的通信，即发送 HTTP 请求。
9. 接下来双方使用对称加密算法进行加密，用 hash secret 对 HTTP 报文做一次运算生成一个 MAC（相当于摘要），附在 HTTP 报文的后面，然后用 session-secret 加密所有数据（HTTP + MAC），然后发送。接收方则先用 session-secret 解密数据，然后得到 HTTP + MAC，再用相同的算法计算出自己的 MAC，如果两个 MAC 相等，证明数据没有被篡改。

[Home](../../README.md)