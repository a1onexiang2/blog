[Home](../../README.md)

# Standard

## Http
HTTPS 由两部分组成：HTTP、SSL/TLS，即在 HTTP 上又加了一层处理加密信息的模块。使用 HTTPS 协议之后在网络上传输的数据是加密的密文，即便被拦截，没有密钥进行解密的话也就是一串乱码。端口号是 443。
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
![image](https://user-images.githubusercontent.com/8423120/46252536-7ca60f00-c49c-11e8-8ded-658681d2254e.png)


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
![image](https://user-images.githubusercontent.com/8423120/46252537-8596e080-c49c-11e8-8721-70946390111e.png)

#### Http 请求方法
![image](https://user-images.githubusercontent.com/8423120/46252516-26d16700-c49c-11e8-98d2-605c6654998e.png)

#### Keep-Alive
我们知道 HTTP 协议采用 “请求 - 应答” 模式，当使用普通模式，即非 Keep-Alive 模式时，每个请求/应答客户和服务器都要新建一个连接，完成之后立即断开连接（HTTP 协议为无连接的协议）；当使用 Keep-Alive 模式（又称持久连接、连接重用）时，Keep-Alive 功能使客户端到服务器端的连接持续有效，当出现对服务器的后继请求时，Keep-Alive 功能避免了建立或者重新建立连接。
在 HTTP/1.1 版本中，默认情况下所有连接都被保持，如果加入 "Connection: close" 才关闭。目前大部分浏览器都使用 HTTP/1.1 协议，也就是说默认都会发起 Keep-Alive 的连接请求了，所以是否能完成一个完整的 Keep-Alive 连接就看服务器设置情况。
HTTP Keep-Alive 简单说就是保持当前的 TCP 连接，避免了重新建立连接。
HTTP 长连接不可能一直保持，例如 Keep-Alive: timeout=5, max=100，表示这个 TCP 通道可以保持 5 秒，max=100，表示这个长连接最多接收 100 次请求就断开。
HTTP 是一个无状态协议，这意味着每个请求都是独立的，Keep-Alive 没能改变这个结果。另外，Keep-Alive 也不能保证客户端和服务器之间的连接一定是活跃的，在 HTTP1.1 版本中也如此。唯一能保证的就是当连接被关闭时你能得到一个通知，所以不应该让程序依赖于 Keep-Alive 的保持连接特性，否则会有意想不到的后果。

#### 首部字段
首部字段是由首部字段名和字段值构成的，中间用冒号 “：” 分隔。字段值对应单个 HTTP 首部字段可以有多个值。当 HTTP 报文首部中出现了两个或以上具有相同首部字段名的首部字段时，这种情况在规范内尚未明确，根据浏览器内部处理逻辑的不同，优先处理的顺序可能不同，结果可能并不一致。
首部字段分为以下几种类型：
- **通用首部字段**
请求报文和响应报文两方都会使用的首部。
    首部字段名 | 说明
    -- | --
    Cache-Control | 控制缓存的行为
    Connection | 逐挑首部、连接的管理
    Date | 创建报文的日期时间
    Pragma | 报文指令
    Trailer | 报文末端的首部一览
    Transfer-Encoding | 指定报文主体的传输编码方式
    Upgrade | 升级为其他协议
    Via | 代理服务器的相关信息
    Warning	| 错误通知
- **请求首部字段**
从客户端向服务器端发送请求报文时使用的首部。补充了请求的附加内容、客户端信息、响应内容相关优先级等信息
    首部字段名 | 说明
    -- | --
    Accept | 用户代理可处理的媒体类型
    Accept-Charset | 优先的字符集
    Accept-Encoding | 优先的内容编码
    Accept-Language | 优先的语言（自然语言）
    Authorization | Web 认证信息
    Expect | 期待服务器的特定行为
    From | 用户的电子邮箱地址
    Host | 请求资源所在服务器
    If-Match | 比较实体标记（ETag）
    If-Modified-Since | 比较资源的更新时间
    If-None-Match | 比较实体标记（与 If-Macth 相反）
    If-Range | 资源未更新时发送实体 Byte 的范围请求
    If-Unmodified-Since | 比较资源的更新时间 (与 If-Modified-Since 相反)
    Max-Forwards | 最大传输逐跳数
    Proxy-Authorization | 代理服务器要求客户端的认证信息
    Range | 实体的字节范围请求
    Referer | 对请求中 URI 的原始获取方
    TE | 传输编码的优先级
    User-Agent | HTTP 客户端程序的信息
- **响应首部字段**
从服务器端向客户端返回响应报文时使用的首部。补充了响应的附加内容，也会要求客户端附加额外的内容信息。
    首部字段名 | 说明
    -- | --
    Accept-Ranges | 是否接受字节范围请求
    Age | 推算资源创建经过时间
    ETag | 资源的匹配信息
    Location | 令客户端重定向至指定 URI
    Proxy-Authenticate | 代理服务器对客户端的认证信息
    Retry-After | 对再次发起请求的时机要求
    Server | HTTP 服务器的安装信息
    Vary | 代理服务器缓存的管理信息
    WWW-Authenticate | 服务器对客户端的认证信息
- **实体首部字段**
针对请求报文和响应报文的实体部分使用的首部。补充了资源内容更新时间等与实体有关的的信息。
    首部字段名 | 说明
    -- | --
    Allow | 资源可支持的 HTTP 方法
    Content-Encoding | 实体主体适用的编码方式
    Content-Language | 实体主体的自然语言
    Content-Length | 实体主体的大小（单位：字节）
    Content-Location | 替代对应资源的 URI
    Content-MD5 | 实体主体的报文摘要
    Content-Range | 实体主体的位置范围
    Content-Type | 实体主体的媒体类型
    Expires | 实体主体过期的日期时间
    Last-Modified | 资源的最后修改日期时间
- **为 Cookie 服务的首部字段**
    首部字段名 | 说明 | 首部类型
    -- | -- | --
    Set-Cookie | 开始状态管理所使用的 Cookie 信息 | 响应首部字段
    Cookie | 服务器接收到的 Cookie 信息 | 请求首部字段

#### 响应状态码
状态码有以下几种类别：
状态码 | 类别 | 原因短语
-- | -- | --
1xx | Informational（信息性状态码） | 接收的请求正在处理
2xx | Success（成功状态码） | 请求正常处理完毕
3xx | Redirection（重定向状态码） | 需要进行附加操作以完成请求
4xx | Client Error（客户端错误状态码） | 服务器无法处理请求
5xx | Server Error（服务器错误状态码） | 服务器处理请求出错
其中常用的状态码有以下几种：
- 200 OK
表示从客户端发来的请求在服务器端已被正常处理。
- 204 No Content
代表服务器接收的请求已成功处理，但在返回的响应报文中不含实体的主体部分。另外，也不允许返回任何实体的主体。
一般在只需要从客户端向服务器端发送消息，而服务器端不需要向客户端发送新消息内容的情况下使用。
- 206 Partial Content
表示客户端进行了范围请求，而服务器成功执行了这部分的 GET 请求。响应报文中包含由 Content-Range 首部字段指定范围的实体内容。
- 301 Moved Permanently
永久性重定向。表示请求的资源已被分配了新的 URI。以后应使用资源现在所指的 URI。也就是说，如果已经把资源对应的 URI 保存为书签了，这时应该按 Location 首部字段提示的 URI 重新保存。
- 302 Found
临时性重定向。表示请求的资源已被分配了新的 URI，希望用户（本次）能使用新的 URI 访问。
和 301 Moved Permanently 状态码相似，但 302 Found 状态码代表资源不是被永久移动，只是临时性质的。换句话说，已移动的资源对应的 URI 将来还有可能发生改变。
- 303 See Other
表示由于请求的资源存在着另一个 URI，应使用 GET 方法定向获取请求的资源。
303 See Other 和 302 Found 状态码有着相同的功能，但 303 See Other 状态码明确表示客户端应采用 GET 方法获取资源，这点与 302 Found 状态码有区别。
- 304 Not Modified
表示客户端发送附带条件的请求时，服务器端允许请求访问的资源，但未满足条件的情况。
304 Not Modified 状态码返回时，不包含任何响应的主体部分。
304 Not Modified 虽然被划分到 3xx 类别中，但和重定向没有关系。
- 307 Temporary Redirect
临时重定向。该状态码与 302 Found 有着相同的含义。
- 400 Bad Request
表示请求报文中存在语法错误。当错误发生时，需修改请求的内容后再次发送请求。
另外，浏览器会像 200 OK 一样对待该状态码。
- 401 Unauthorized
表示发送的请求需要有通过 HTTP 认证（BASIC 认证、DIGEST 认证）的认证信息。
另外，若之前已进行过 1 次请求，则表示用户认证失败。
返回含有 401 Unauthorized 的响应必须包含一个适用于被请求资源的 WWW-Authenticate 首部用以质询（challenge）用户信息。
- 403 Forbidden
表明对请求资源的访问被服务器拒绝了。服务器端没有必要给出详细的拒绝理由，当然也可以在响应报文的实体主体部分对原因进行描述。
- 404 Not Found
表明服务器上无法找到请求的资源。除此之外，也可以在服务器端拒绝请求且不想说明理由的时候使用。
- 500 Internal Server Error
表明服务器端在执行请求时发生了错误。也可能是 Web 应用存在的 bug 或某些临时的故障。
- 503 Service Unavailable
表明服务器暂时处于超负载或正在进行停机维护，现在无法处理请求。如果事先得知解除以上状况需要的时间，最好写入 Retry-After 首部字段再返回给客户端。

#### 分片传输 & 断点续传
在请求资源时，如果服务器响应头中有 `accept-ranges=bytes`，表明服务器支持 bytes 类型得数据断点续传。如果为空或者 none，可能不支持断点续传。
HTTP/1.1 协议（RFC2616）中定义了 HTTP 头 Range 和 Content-Range 字段。Range 用于指定资源的 byte 范围。
响应信息如果返回状态码 206，表明已经处理了部分请求，对于多重范围的范围请求，响应会在首部字段 Content-Type 中标明 multipart/byteranges。
终端在发起续传请求时应该在 HTTP 头中申明 If-Match 或者 If-Modified-Since 字段，帮助服务端判别文件变化。
另外 RFC2616 中同时定义有一个 If-Range 头，终端如果在续传时使用 If-Range。If-Range 中的内容可以为最初收到的 ETag 头或者是 Last-Modfied 中的最后修改时候。服务端在收到续传请求时，通过 If-Range 中的内容进行校验，校验一致时返回 206 的续传回应，不一致时服务端则返回 200 回应，回应的内容为新的文件的全部数据。

#### Http 实体编码
HTTP 应用程序有时在发送之前需要对内容进行编码。例如，在把很大的 HTML 文档发送给通过慢速连接上来的客户端之前，服务器可能会对其进行压缩，这样有助于减少传输实体的时间。服务器还可以把内容搅乱或加密，以此来防止未授权的第三方看到文档的内容。这种类型的编码是在发送方应用到内容之上的。当内容经过内容编码后，编好码的数据就放在实体主体中，像往常一样发送给接收方。
常见的编码类型有以下几种：
编码方式 | 描述
-- | --
gzip | 表明实体采用 GNU zip 编码
compress | 表明实体采用 Unix 的文件压缩程序
deflate | 表明实体采用 zlib 的格式压缩的
identity | 表明没有对实体进行编码，当没有 Content-Encoding 首部字段时默认采用此编码方式

[Home](../../README.md)