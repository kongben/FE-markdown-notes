## 什么是CSRF攻击？
网上冲浪这些年，经常能听到的一句话：“别点那个链接，小心有病毒！”。那么问题来了，我就点击一个链接怎么就能染上病毒了呢？这个‘病毒’是怎样盗走我的账号的？

CSRF 英文全称是 Cross-site request forgery，所以又称为“跨站请求伪造”，是指黑客引诱用户打开黑客的网站，在黑客的网站中，利用用户的登录状态发起的跨站请求。简单来讲，CSRF 攻击就是黑客利用了用户的登录状态，并通过第三方的站点来做一些坏事。

一个远古例子：

遥远的2007年，David 使用浏览器无打开了 Gmail 中的一份邮件，并点击了该邮件中的一个链接。过了几天，David 就发现他的域名被盗了。虽然 David 最后通过走关系（网上冲浪认识的关系），之所以被盗他的域名，就是因为无意间点击的那个链接。

那么黑客是怎么盗走 David的域名的呢？

![盗取域名的流程图](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6c906bd5affa469fab9b6443cafca2b2~tplv-k3u1fbpfcp-zoom-1.image)

1. 首先 David 使用浏览器发起登录 Gmail，然后 Gmail 服务器返回一些登录状态Cookie、Session 等给浏览器。
2. 接着黑客引诱 David 去打开他的链接，然后在 xxx.com 页面中，黑客编写好了一个邮件过滤器的请求去访问Gmail的接口，设置好了新的邮件过滤功能，因为在相同浏览器中，请求可以拿到服务器返回Gmail的一些登录状态Cookie等鉴权信息，并顺利设置好所有的邮件都转发到黑客的邮箱中。
3. 黑客通过邮箱找回域名账户的密码，然后将域名转出到黑客的账户了。

以上就是 David 的域名被盗事件之谜的真相，其中前两步过程就是 CSRF 攻击。

David 在要回了他的域名之后，也将整个攻击过程分享到他的站点上了，如果你感兴趣的话，可以参考[该链接](https://www.davidairey.com/google-gmail-security-hijack)（放心这个链接不会对你进行CSRF攻击 狗头保命）。

## 如何进行CSRF攻击？
有三种方式可以进行CSRF攻击。

假设
现在有一个银行转账接口
```
#同时支持POST和Get
#接口 
https://www.bank.com/sendmoney
#参数
##目标用户
user
##目标金额
number
```
### 1.自动发起GET请求
黑客最容易的就是在用户无察觉的情况下发送GET请求。
```html
<!DOCTYPE html>
<html>
  <body>
    <h1>黑客的站点：CSRF攻击演示</h1>
    <img src="https://www.bank.com/sendmoney?user=hacker&number=100">
  </body>
</html>
```
当用户访问链接时，转账的请求接口隐藏在 img 标签内，浏览器以为这是一张图片资源。发送请求，服务器就会认为该请求是一个转账请求，于是用户账户上的 100 块就被转移到黑客的账户上去了。

### 2.自动发起 POST 请求
对于接口类型使用post的接口，在站点上伪造 POST 请求，当用户点开链接后，会自动提交 POST 请求。
```html
<!DOCTYPE html>
<html>
<body>
  <h1>黑客的站点：CSRF攻击演示</h1>
  <form id='hacker-form' action="https://time.geekbang.org/sendcoin" method=POST>
    <input type="hidden" name="user" value="hacker" />
    <input type="hidden" name="number" value="100" />
  </form>
  <script> document.getElementById('hacker-form').submit(); </script>
</body>
</html>
```
### 3.诱骗链接
这种常用于邮箱的邮件或者论坛上，会故意引诱用户去点击链接。
```html
<div>
  <img width=150 src=http://images.xuejuzi.cn/1612/1_161230185104_1.jpg> </img> </div> <div>
  <a href="https://www.bank.com/sendmoney?user=hacker&number=100" taget="_blank">
    点击下载美女照片
  </a>
</div>
```

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/44c55b4681b8420f88768072cd0d7e30~tplv-k3u1fbpfcp-zoom-1.image)
[点击下载美女照片](https://juejin.im/)

就问你心不心动？（狗头）

黑客页面上放了一张美女图片，下面放了图片下载地址，而这个下载地址实际上是黑客用来转账的接口，一旦用户点击了这个链接，那么他的钱就被转到黑客账户上了。

以上三种就是黑客经常采用的CSRF攻击方式。

当然前提是用户登录了银行，以上三种 CSRF 攻击方式中的任何一种发生时，那么服务器都会将一定金额的钱发送到黑客账户。

到这里，相信你已经知道什么是 CSRF 攻击了。和 XSS 不同的是，CSRF 攻击不需要将恶意代码注入用户的页面，仅仅是利用服务器的漏洞和用户的登录状态来实施攻击。

## 如何阻止CSRF攻击？
首先，让我们总结一下能施展CSRF攻击的几个前提
1. 目标站点一定要有 CSRF 漏洞；即没有完善的鉴权或二次确认。
2. 用户要登录过目标站点，并且在浏览器上保持有该站点的登录状态。
3. 需要用户打开一个第三方站点。

可以注意到，与 XSS 攻击不同，CSRF 攻击不会往页面注入恶意脚本，因此黑客是无法通过 CSRF 攻击来获取用户页面数据的；

其最关键的一点是要能找到服务器请求漏洞，所以说对于 CSRF 攻击我们主要的防护手段是提升服务器的安全性。

提升服务器安全性的几个方法：

### 1. 使用Cookie的SameSite属性

在 HTTP 响应头中，设置 Cookie 时，带上 SameSite 选项，可以防止第三方站点使用Cookie。

SameSite 选项通常有 Strict、Lax 和 None 三个值。

+ Strict 最为严格。如果 SameSite 的值是 Strict，那么浏览器会完全禁止第三方 Cookie。简言之，如果你从极客时间的页面中访问 InfoQ 的资源，而 InfoQ 的某些 Cookie 设置了 SameSite = Strict 的话，那么这些 Cookie 是不会被发送到 InfoQ 的服务器上的。只有你从 InfoQ 的站点去请求 InfoQ 的资源时，才会带上这些 Cookie。
+ Lax 相对宽松一点。在跨站点的情况下，从第三方站点的链接打开和从第三方站点提交 Get 方式的表单这两种方式都会携带 Cookie。但如果在第三方站点中使用 Post 方法，或者通过 img、iframe 等标签加载的 URL，这些场景都不会携带 Cookie。
+ 而如果使用 None 的话，在任何情况下都会发送 Cookie 数据。

严格程度分别是**全部不允许**第三方>仅允许**GET**请求>**全部允许**

SameSite具体使用方式，参考[链接](https://web.dev/samesite-cookies-explained)(放心这个也没有CSRF攻击，仿佛CSRF PTSD)
### 2. 验证请求的来源站点
当使用post请求时，HTTP头会自带**origin**字段，服务器可以通过orig知晓从那个第三方站点请求，可以做白名单加以过滤。

### 3. CSRF Token
简单来说就是Cookie不安全，可以手动设置一个状态字段，浏览器请求页面数据的时候带过去，请求关键数据的时候通过请求字段携带状态字段。因为CSRF不能访问用户页面数据，就可以保证请求是合法的。就算黑客通过CSRF发出了请求，服务器也会因为 CSRF Token 不正确而拒绝请求。

## 总结