导航阶段是指用户发出 URL 请求到页面开始解析的这个过程，就叫做导航。

## URL导航流程

1. 当用户输入`juejin.im`后，回车。浏览器会根据输入的URL规则，对URL进行补全使用协议:`http://juejin.im`

2. 如果用户是在新tab回车，则直接进入导航流程，如果输入URL的tab有监听`beforeunload`，则会回调，诸如提示用户有未提交的表单。

3. 接下来就会进入请求流程，浏览器tab页面会变成loading状态，浏览器进程会通过IPC通知网络请求进程，网络请求才正式开始。

4. 如果当前请求在浏览器内有缓存，请求进程则不会发出真实请求，直接返回资源给浏览器进程。如果没有命中缓存，就会进入下一步。

5. 请求会访问DNS，去解析域名，先访问本地host看是否有本地缓存。再去访问DNS服务器，通过UDP从底层从下往上一层层的寻找可用的服务。直到根DNS。若没有发现IP地址，则返回错误情况的资源。若成功通过DNS将域名解析就能获得目标服务器的IP。

6. 拿到了目标服务器IP地址`36.110.186.250`，就可以通过TCP三次握手建立可靠连接，发送HTTP请求：`https://36.110.186.250`

7. 通过发送HTTP请求，访问`https://36.110.186.250`,则会返回`301`永久重定向。表示应该使用https协议。浏览器的网络进程就会重新将请求重定向到`https://juejin.im/`，启用HTTPS协议。

   ```http
   HTTP/1.1 301 Moved Permanently
   Server: nginx
   Date: Wed, 26 Aug 2020 02:53:00 GMT
   Content-Type: text/html
   Content-Length: 178
   Connection: keep-alive
   Location: https://juejin.im/
   x-tt-trace-host: 01a7014f7ed11408be61e299a6adbff8412246242c140335d918940ff023f8dde055bd25b0c6a8d6bda2c2376ff2feea70
   x-tt-trace-tag: id=00;cdn-cache=miss
   
   <html>
   <head><title>301 Moved Permanently</title></head>
   <body bgcolor="white">
   <center><h1>301 Moved Permanently</h1></center>
   <hr><center>nginx</center>
   </body>
   </html>
   ```

8. 使用HTTPS协议，访问`https://juejin.im/`,重走1，2步骤。通过HTTPS握手，加密传输请求`https://36.110.186.250`。

```http
HTTP/2 200 
server: nginx
date: Wed, 26 Aug 2020 02:59:56 GMT
content-type: text/html; charset=utf-8
content-length: 85006
vary: Accept-Encoding
x-powered-by: Express
x-tt-logid: 202008261059560101980600535C397E16
etag: "14c0e-WDqcvVNFdnAOda+zn1ipX62mo9g"
accept-ranges: none
server-timing: inner; dur=24, pp;dur=3, total;dur=22;desc="Nuxt Server Time"
vary: Accept-Encoding
x-tt-trace-host: 01a7014f7ed11408be61e299a6adbff841ff9dce4c42a8eb02770760bd87c92a68073fd989412c4bf563db545beb35a8df9ac47cd20c3e786ab3cfa73be4817c4d
x-tt-trace-tag: id=00;cdn-cache=miss

<!doctype html>
...
```

9. 网络请求进程会根据`Content-Type`头的值，告诉浏览器主进程返回的类型，浏览器主进程则会分配给不同进程进行操作。若值`text/html`则表示是返回html格式内容，会返回给主进程进行渲染流程，如果返回`application/octet-stream`,则表示是下载资源，浏览器主进程就会把该请求交给下载器，并结束目前导航流程。
10. 准备渲染流程，通常每一个tab是单独的一个渲染进程。如果多个tab页面是同一站点（即同协议，同根域名）,就会复用同一个渲染进程。
11. 确定好渲染进程后，网络进程会将html资源提交给渲染进程。
   1. 发送CommitNavigation消息到渲染进程，发送CommitNavigation时会携带响应头、等基本信息。
   2. 渲染进程接收到CommitNavigation消息之后，便开始准备接收HTML数据，接收数据的方式是直接和网络进程建立数据管道
   3. 最后渲染进程会像浏览器进程“确认提交”，这是告诉浏览器进程，说我已经准备好接受和解析页面数据了
   4. 最后浏览器进程更新页面状态。包括了安全状态、地址栏的 URL、前进后退的历史状态，并更新 Web 页面。