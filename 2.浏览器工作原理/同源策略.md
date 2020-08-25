# HTTP同源策略

## 何为同源？

域名、协议、端口完全一致即为同源。

+ [http://www.juejin.com](https://www.juejin.com) 和[http://juejin.com](https://juejin.com)
   不同源，因为域名不同

+ http://www.bilibili.tv和http://www.bilibili.com

  不同源，因为域名不同

+ http://localhost:3000 和  http://localhost:3001
   不同源,因为端口不同

+ http://qq.com 和https://qq.com
   不同源，因为协议不同

+ https://www.pixiv.net 和 https://www.pixiv.net/manage/illusts/
   同源，因为域名，协议，端口都相同

# 何为策略？

策略主要限制js的能力

1.无法读取非同源的 cookie、Storage、indexDB的内容

2.无法读取非同源的DOM

3.无法发送非同源的AJAX，更加准确的说应该是发送了请求但被浏览器拦截了。

#为什么会有同源策略？

> 为了保护用户数据安全

1.为了防止恶意网页可以获取其他网站的本地数据。

2.为了防止恶意网站iframe其他网站的时候，获取数据。

3.为了防止恶意网站在自已网站有访问其他网站的权利，以免通过cookie免登，拿到数据。

# 跨域问题

前后端分离，导致前端页面地址和后端API不是同源的，例如前端地址为baidu.com,后端API为api.baidu.com。

### 常见的跨域方法

1.CORS

​	CORS（跨域资源共享）使用专用的HTTP头，服务器（api.baidu.com）告诉浏览器，特定URL（baidu.com）的ajax请求可以直接使用，不会激活同源策略。

2.JSONP

​	这个方案相当于黑魔法，因为js调用（实际上是所有拥有src属性的 <\script>、<\img>、<\iframe>）是不会经过同源策略，例如baidu.com引用了CDN的jquery。所以我通过调用js脚本的方式，从服务器上获取JSON数据绕过同源策略。

3.nginx反向代理

​	当你访问baidu.com/api/login的时候，通过在baidu.com的nginx服务器会识别你是**/api**的资源，会自动代理到api.baidu.com/login，浏览器本身是不知道我实际上是访问的api.baidu.com的数据，和前端资源同源，所以也就不会触发浏览器的同源策略。