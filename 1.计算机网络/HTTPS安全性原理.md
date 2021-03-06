# 前置知识

如果后面讲解不懂的时候，可以看前置知识中的名词解释。

+ 对称加密

  一把钥匙，就能加密解密一段消息，特点速度快。

+ 非对称加密（RSA）

  有两把钥匙。

  特点：
  
  	用私钥加密的数据，只有对应的公钥才能解密。
  	用公钥加密的数据， 只有对应的私钥才能解密。
  
  缺点：
  
  幂等复杂度。比对称加密慢很多很多。

+ 消息包

  服务器RSA算法的公钥数据、其个人信息和其他的信息的集合
![](https://user-gold-cdn.xitu.io/2020/3/14/170d9342cc05599a?w=139&h=120&f=png&s=5843)

+ 消息摘要

  将消息包HASH后得到的数据
  

![](https://user-gold-cdn.xitu.io/2020/3/14/170d9347556b8775?w=171&h=170&f=png&s=7173)

+ 数字签名

  将消息摘要（也就是HASH值）用CA（认证机构）的私钥加密的数据
  

![](https://user-gold-cdn.xitu.io/2020/3/14/170d934f2df335d5?w=188&h=219&f=png&s=10429)

+ 数字证书

  RSA算法消息包和数字签名的集合

![](https://user-gold-cdn.xitu.io/2020/3/14/170d93549358c831?w=349&h=237&f=png&s=13058)

# HTTPS加密流程

> 如何才叫安全的会话呢？

HTPPS的会话只要是建立在一个**安全的对称加密**的会话上就是安全的。

> 那么如何在明文的前提下把对称加密的钥匙传送给浏览器呢？

使用TLS（**传输层安全性协议**Transport Layer Security）

TSL的大致意思：

![](https://user-gold-cdn.xitu.io/2020/3/14/170d93624c4e0eef?w=637&h=522&f=png&s=52969)

共3步

1.浏览器请求服务器（明文情况）

2.服务器返回一个数字证书

![](https://user-gold-cdn.xitu.io/2020/3/14/170d93549358c831?w=349&h=237&f=png&s=13058)

数字证书包含一个含有服务器RSA公钥的信息包，和数字签名，数字签名是经过CA私钥加密的信息摘要，而信息摘要是信息包HASH的值。

浏览器通过系统上可信任的CA机构提供的公钥解密数字签名，数字签名解密出来的信息摘要和未加密的信息自己做HASH做一致性测试，排除信息包中公钥数据被篡改的可能。浏览器就收获了一个绝对安全的服务器的RSA公钥。

3.浏览器通过通过服务器的RSA公钥来加密一个随机的对称加密的密钥-会话密钥（session key）,传输给服务器，就建立一个安全的对称加密的会话。

## 疑问？

在第2步花了老大劲就是为了浏览器得到一个绝对安全的RSA公钥，可却还是用的系统内置的CA RSA公钥解密。为什么不直接用CA机构的公钥加密session key，反而麻烦的用RSA来传送RSA的公钥？

    答：为了防止万一CA的私钥泄密导致在网的所有HTTPS会话（仅使用CA公钥加密过的）被泄密。TLS设计需要满足PFS（向前安全性 Perfect Forward Secrecy）。