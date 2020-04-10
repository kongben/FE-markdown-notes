HTTP全称是超文本传输协议，所以其实HTTP被创造出来的需求是传输HTML也就是超文本标记语言。

### HTTP/0.9 -单行协议

最初，HTTP并没有版本号，在决定增加版本号的时候，把之前的版本称为0.9

HTTP 0.9 request

请求由单行指令构成，只能使用`GET`方法，其后接目标资源路径。

> GET /mypage.html

HTTP 0.9 response

响应也简单极了，只包含HTML文档本身。

```html
<HTML>
这是一个非常简单的HTML页面
</HTML>
```

#### 总结：

HTTP/0.9

请求是单行指令，只能传输HTML文档，无法传输其他文件类型。

响应只返回HTML文档。没有状态码，所以一旦出现问题，就会传输问题日志的HTML文件。

### HTTP/1.0 -可扩展性

相对于0.9版本，1.0版本新增了许多feature

+ 新增HTTP头的概念，允许传输元数据，提升可扩展性。
+ 在HTTP头新增Content-Type字段，可以让浏览器具有传输其他资源类型的能力

+ 请求
+ 1.协议版本信息会随着请求发送（放在GET行后）
+ 响应
+ 新增状态码，使浏览器可以判断请求的状态，作出更新或者缓存。

```http
GET /mypage.html HTTP/1.0
User-Agent: NCSA_Mosaic/2.0 (Windows 3.1)  //HTTP头-用户信息

200 OK                              //状态码
Date: Tue, 15 Nov 1994 08:12:31 GMT //HTTP头-时间
Server: CERN/3.0 libwww/2.17        //HTTP头-服务
Content-Type: text/html             //HTTP头-资源类型
<HTML> 
一个包含图片的页面
  <IMG SRC="/myimage.gif">
</HTML>
```

### HTTP/1.1 – 标准化

在1.0发布后的几个月，HTTP1.1标准发布。相对于1.0 新增了若干featrue。

+ 新增连接保持，使之可复用。`Connection: Keep-Alive`
+ 新增管线化（**pipelining**）操作，允许在第一个请求响应发送之前，就发送第二个请求，减少了通信延迟。
+ 新增响应分块`Transfer-Encoding:chunked`。
+ 新增缓存机制。
+ 新增协商机制。（语言，编码，类型）
+ 新增`Host`，使不同域名能配置在同一个IP地址上。

```http
GET /en-US/docs/Glossary/Simple_header HTTP/1.1
Host: developer.mozilla.org
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.9; rv:50.0) Gecko/20100101 Firefox/50.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://developer.mozilla.org/en-US/docs/Glossary/Simple_header

200 OK
Connection: Keep-Alive
Content-Encoding: gzip
Content-Type: text/html; charset=utf-8
Date: Wed, 20 Jul 2016 10:55:30 GMT
Etag: "547fa7e369ef56031dd3bff2ace9fc0832eb251a"
Keep-Alive: timeout=5, max=1000
Last-Modified: Tue, 19 Jul 2016 00:59:33 GMT
Server: Apache
Transfer-Encoding: chunked
Vary: Cookie, Accept-Encoding

(content)  //保持握手信息


GET /static/img/header-background.png HTTP/1.1
Host: developer.cdn.mozilla.net
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.9; rv:50.0) Gecko/20100101 Firefox/50.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://developer.mozilla.org/en-US/docs/Glossary/Simple_header

200 OK
Age: 9578461
Cache-Control: public, max-age=315360000
Connection: keep-alive
Content-Length: 3077
Content-Type: image/png
Date: Thu, 31 Mar 2016 13:34:46 GMT
Last-Modified: Wed, 21 Oct 2015 18:27:50 GMT
Server: Apache

(image content of 3077 bytes)
```

### HTTPS-安全传输

HTTP在TCP/IP协议上进行传输。在1994年，网景在HTTP上，新增了一个额外的传输层SSL，加密数据，标准化后称为TLS。所以HTTP + TLS = HTTPS。

### HTTP/2-为了更优异的表现

新feature

+ 修改文本协议为二进制协议，不再可读，也不能无障碍创建。
+ 新增复用，并行请求能在统一链接中处理。移除1.x中的顺序和阻塞的约束。
+ 压缩headers。
+ 允许服务器和客户端缓存中填充数据。

