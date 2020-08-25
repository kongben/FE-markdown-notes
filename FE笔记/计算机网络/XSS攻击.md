### 什么是XSS？

xss全称Cross Site Scripting（跨站脚本），为了与“CSS”区别，就使用XSS作为简称。XSS 攻击是指hacker往 HTML 文件中注入恶意脚本，从而在用户浏览页面时运行恶意脚本对用户实施攻击的一种手段。

当页面被注入了恶意 JavaScript 脚本时，浏览器是无法区分这些脚本是否是被恶意注入的还是正常的页面脚本，所以恶意注入 JavaScript 脚本也拥有所有的脚本权限。

> 如果页面被注入了恶意 JavaScript 脚本，恶意脚本都能做哪些事情？

+ 窃取 Cookie 信息
  + 通过恶意js脚本获取 Cookie 信息，然后通过ajax加上CORS功能将数据发送给恶意服务器，恶意服务器拿到用户的 Cookie 信息之后，就可以模拟用户的登录，然后进行账户操作。

+ 监听用户行为
  + 通过恶意js脚本，可以做到监听用户各种事件，比如获取登陆的键入字符串完成hack用户信息。
+ 更改DOM结构
  + 比较常见的就是通过运营商或者路由器添加浮窗广告，增收自身收入。

### 如何进行XSS攻击？

XSS主要分为三种类型，存储型 XSS 攻击，反射型XSS攻击和基于DOM的XSS攻击

#### 1.存储型 XSS 攻击

![存储型 XSS 攻击](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2b63ee3ac3ce483f8e1264704c2e5e4b~tplv-k3u1fbpfcp-zoom-1.image)

通过上图，我们可以看出存储型 XSS 攻击大致需要经过如下步骤：

1. 首先hacker利用站点漏洞将一段恶意 JavaScript 代码提交到网站的数据库中；
2. 然后用户向网站请求包含了恶意 JavaScript 脚本的页面；
3. 当用户浏览该页面的时候，恶意脚本就会将用户的 Cookie 信息等数据上传到服务器。

**一个例子：**

以2015年喜马拉雅就被曝出的存储型 XSS 漏洞为例，看看整个XSS攻击流程。

在用户设置专辑名称时，服务器对关键字过滤不严格，使得在编辑专辑名称的 时候可以设置为一段 JavaScript，如下图所示：

![提交恶意脚本](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/af88aeaf327343969833e58918abe926~tplv-k3u1fbpfcp-zoom-1.image)

当hacker将专辑名称设置为一段 JavaScript 代码并提交时，喜马拉雅的服务器会保存该段 JavaScript 代码到数据库中。然后当用户打开hacker设置的专辑时，这段代码就会在用户的页面里执行（如下图）

![用户打开含有恶意代码的页面](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/29a5e2e9997d4972853196496ba28ab7~tplv-k3u1fbpfcp-zoom-1.image)

恶意脚本可以通过 XMLHttpRequest 或者 Fetch 将用户的 Cookie 数据上传到黑客的服务器。

![恶意服务器上的用户cookie](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c9593a162a594a148182802ea62a014c~tplv-k3u1fbpfcp-zoom-1.image)

再通过手动设置cookie就可以绕过，直接登陆喜马拉雅。

![成功hack](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/966c8472a903469f8905725c99530f1f~tplv-k3u1fbpfcp-zoom-1.image)


以上就是存储型 XSS 攻击的一个典型案例。这是乌云网在 2015 年曝出来的，虽然乌云网由于某些原因被关停了。默哀。

#### 2.反射型 XSS 攻击

在一个反射型 XSS 攻击过程中，hacker需要找到一个接口漏洞，用户给服务器发送的一些参数后，服务器没有经过处理，直接原封返回了部分参数，就给了黑客可乘之机，把恶意 JavaScript 脚本当参数发给服务器，服务器直接返回了这个脚本字符串，在浏览器DOM解析器中就顺利引入恶意脚本达成hack。

**再举个例子**

现在我们有一个简单的服务

```js
var express = require('express');
var router = express.Router();
/* GET home page. */
router.get('/', function(req, res, next) {
	res.render('index', {
  	title: 'Express',
    xss:req.query.xss 
  });
});
module.exports = router;
```

接受一个参数，并返回回去渲染成html。

```html
<!DOCTYPE html>
<html>
<head>
  <title><%= title %></title>
  <link rel='stylesheet' href='/stylesheets/style.css' />
</head>
<body>
  <h1><%= title %></h1>
  <p>Welcome to <%= title %></p>
  <div>
      <%- xss %>
  </div>
</body>
</html>
```

访问：**http://localhost:3000/?xss=123**

![正常访问](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d64e3e1cdeb74872bfc6d0b331f07f75~tplv-k3u1fbpfcp-zoom-1.image)

hacker找到了这个漏洞，拼凑了一个这样的url：**http://localhost:3000/?xss=<script src='xxx'></script>**,然后诱骗你点击后，在页面上就会运行恶意脚本。

值得注意的是，储存型XSS和反射性XSS有一个特别明显的区别就是反射型XSS是不会在服务器中存储脚本的，所有诱骗用户的方式也不同，hack储存型XSS的工作量主要在如果上传恶意脚本，剩下的就要等用户点击正常网页，反射型则需要诱骗用户主动点击包含漏洞url。

#### 3.基于DOM的XSS攻击

一般来说，hacker是劫持不了正常的传输网络，但如果有内鬼能通过中间人代理劫持html传输，就可以修改html文件在其中任意穿插恶意脚本，再发送给用户。一般来说，这种情况出现于运营商中间，所以基于DOM的XSS我觉得应该称为基于内鬼的XSS攻击（狗头）。

### 如何阻止 XSS 攻击

1. 存储型和反射型XSS都是服务器没有严格检测用户输入数据，即**不能相信用户的任何输入**。通过给用户输入做过滤，例如将用户输入：`<script src='xxx'></script>`转码，成`&lt;scriptscr='xxx'&gt;&lt;/script&gt;`,这样浏览器的DOM解析器就不能运行恶意脚本。

2. 对于基于DOM的XSS攻击，使用HTTPS进行传输html，可以避免中间人能更改html文件。
3. 充分利用 [CSP]('https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CSP' )，严格实施CSP操作，可以有效防范XSS攻击。
   1. 限制加载其他域下的资源文件，这样即使黑客插入了一个 JavaScript 文件，这个 JavaScript 文件也是无法被加载的；
   2. 禁止向第三方域提交数据，这样用户数据也不会外泄；
   3. 禁止执行内联脚本和未授权的脚本；还提供了上报机制，这样可以帮助我们尽快发现有哪些 XSS 攻击，以便尽快修复问题。
4. 使用 HttpOnly 属性。避免js脚本操作cookie，即使页面被注入了恶意 JavaScript 脚本，也是无法获取到设置了 HttpOnly 的数据。因此一些比较重要的数据我们建议设置 HttpOnly 标志。

### 总结

XSS 攻击就是黑客往页面中注入恶意脚本，然后将页面的一些重要数据上传到恶意服务器。

常见的三种 XSS 攻击模式是存储型 XSS 攻击、反射型 XSS 攻击和基于 DOM 的 XSS 攻击。

这三种攻击方式的共同点是都需要往用户的页面中注入恶意脚本，然后再通过恶意脚本将用户数据上传到黑客的恶意服务器上。而三者的不同点在于注入的方式不一样，有通过服务器漏洞来进行注入的，还有在客户端直接注入的。

针对这些 XSS 攻击，主要有三种防范策略，第一种是通过服务器对输入的内容进行过滤或者转码，第二种是充分利用好 CSP，第三种是使用 HttpOnly 来保护重要的 Cookie 信息。

当然除了以上策略之外，我们还可以通过添加验证码防止脚本冒充用户提交危险操作。而对于一些不受信任的输入，还可以限制其输入长度，这样可以增大 XSS 攻击的难度。