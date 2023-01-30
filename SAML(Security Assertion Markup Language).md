# 一、SAML 介绍

SAML即安全断言标记语言，英文全称是Security Assertion Markup Language。它是一个基于XML的标准，用于在不同的安全域(security domain)之间交换认证和授权数据。在SAML标准定义了身份提供者(identity provider)和服务提供者(service provider)，这两者构成了前面所说的不同的安全域。 SAML是OASIS组织安全服务技术委员会(Security Services Technical Committee)的产品。

SAML（Security Assertion Markup Language）是一个XML框架，也就是一组协议，可以用来传输安全声明。比如，两台远程机器之间要通讯，为了保证安全，我们可以采用加密等措施，也可以采用SAML来传输，传输的数据以XML形式，符合SAML规范，这样我们就可以不要求两台机器采用什么样的系统，只要求能理解SAML规范即可，显然比传统的方式更好。SAML 规范是一组Schema 定义。

可以这么说，在Web Service 领域，schema就是规范，在Java领域，API就是规范。

SAML解决的**最重要**的需求是网页浏览器单点登录（SSO）。单点登录在内部网层面比较常见，（例如使用Cookie），但将其扩展到内部网之外则一直存在问题，并使得不可互操作的专有技术激增。（另一种近日解决浏览器单点登录问题的方法是OpenID Connect协议）

## 1. SAML 作用

SAML 主要包括三个方面：

1.认证申明。表明用户是否已经认证，通常用于单点登录。

2.属性申明。表明 某个Subject 的属性。

3.授权申明。表明 某个资源的权限。

## 2. SAML框架

SAML就是客户向服务器发送SAML 请求，然后服务器返回SAML响应。数据的传输以符合SAML规范的XML格式表示。

SAML 可以建立在SOAP（[简单对象访问协议](https://baike.baidu.com/item/简单对象访问协议)）上传输，也可以建立在其他协议上传输。

因为SAML的规范由几个部分构成：SAML Assertion，SAML Prototol，SAML binding等

## 3. 安全

由于SAML在两个拥有共享用户的站点间建立了信任关系，所以安全性是需考虑的一个非常重要的因素。SAML中的安全弱点可能危及用户在目标站点的个人信息。SAML依靠一批制定完善的安全标准，包括SSL( [安全套接字协议](https://baike.baidu.com/item/安全套接字协议))和X.509([公钥证书的格式标准](https://baike.baidu.com/item/X.509))，来保护SAML源站点和目标站点之间通信的安全。源站点和目标站点之间的所有通信都经过了加密。为确保参与SAML交互的双方站点都能验证对方的身份，还使用了证书。

### SAML协议的安全设计

在SAML协议中，对协议的安全设计十分完整，包协议中定义的客户端、SP及IDP的交互流程(概念详见后文)，并且还使用了密码学对SAML中关键的交互数据进行了签名和加密处理。

### **3.1.对SAML数据进行签名**

 为有效的确保SAML的交互过程中的数据不被伪造和篡改，支持对任何SAML数据进行签名。特别是重要的SAML认证断言。

具体的例如：在某项目中使用了X.509格式的数字证书进行签名，使用了RSA公钥密码算法，SHA-256哈希算法，其密钥长度为1024位。

### **3.2.SAML认证断言的安全性设计：加密和断言有效期**

 在SAML的认证断言中，包含了大量的用户账号信息，因此十分有必要对其进行加密处理。

对SAML认证断言的加密同样支持包括国密算法和AES算法等多种对称加密算法，在本项目中，使用了AES算法，密钥长度为1024位，能够有效的保证SAML认证断言的保密性。

另外，为防止重放攻击，在设计中还对认证断言的有效期进行了限制，认证断言的有效期可配置，建议配置使用较短的有效期。

[什么是SAML - 简书 (jianshu.com)](https://www.jianshu.com/p/5234ffa7272e)