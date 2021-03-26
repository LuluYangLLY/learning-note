## Cookie 基础

### 一、Cookie 起源与发展

#### 1. 诞生背景

众所周知，HTTP的一大特点是无状态。

> HTTP无状态：服务器无法知道两个请求是否来自同一个浏览器，即服务器不知道用户上一次做了什么，每次请求都是完全相互独立。

早期互联网只是用于简单地浏览文档信息、查看门户网站等等，并没有交互这个说法。但是随着互联网慢慢发展，宽带、服务器等硬件设施已经得到很大的提升，互联网允许人们可以做更多的事情，所以交互式Web慢慢兴起，而HTTP无状态的特点却严重阻碍其发展！

> 交互式Web：客户端与服务器可以互动，如用户登录，购买商品，各种论坛等等

不能记录用户上一次做了什么，怎么办？

聪明的程序员们就开始思考：怎么样才能记录用户上一次的操作信息呢？于是有人就想到了隐藏域。

    <input type="hidden" name="field＿name" value="value">

这样把用户上一次操作记录放在form表单的input中，这样请求时将表单提交不就知道上一次用户的操作，但是这样每次都得创建隐藏域而且得赋值太麻烦，而且容易出错！

ps：隐藏域作用强大，时至今日都有很多人在用它解决各种问题！

网景公司当时一名员工Lou Montulli（卢-蒙特利），在1994年将“cookies”的概念应用于网络通信，用来解决用户网上购物的购物车历史记录，而当时最强大的浏览器正是网景浏览器，在网景浏览器的支持下其他浏览器也渐渐开始支持Cookie，到目前所有浏览器都支持Cookie了。

#### 2. Cookie 是什么

前面我们已经知道了Cookie的诞生是为了解决HTTP无状态的特性无法满足交互式web，那它究竟是什么呢？

> Cookie是由服务器发给客户端的特殊信息，而这些信息以文本文件的方式存放在客户端，然后客户端每次向服务器发送请求的时候都会带上这些特殊的信息，用于服务器记录客户端的状态。

Cookie 主要用于以下三个方面：

+ 会话状态管理（如用户登录状态、购物车、游戏分数或其它需要记录的信息）
+ 个性化设置（如用户自定义设置、主题等）
+ 浏览器行为跟踪（如跟踪分析用户行为等）

### 二、Cookie 原理

用户在输入用户名和密码之后，浏览器将用户名和密码发送给服务器，服务器进行验证，验证通过之后将用户信息加密后封装成Cookie放在请求头中返回给浏览器。

    HTTP/1.1 200 OK
    Content-type: text/html
    Set-Cookie: user_cookie=Rg3vHJZnehYLjVg7qi3bZjzg; Expires=Tue, 15 Aug 2019 21:47:38 GMT; Path=/; Domain=.169it.com; HttpOnly

浏览器收到服务器返回数据，发现请求头中有一个：Set-Cookie，然后它就把这个Cookie保存起来，下次浏览器再请求服务器的时候，会把Cookie也放在请求头中传给服务器：

    GET /sample_page.html HTTP/1.1
    Host: www.example.org
    Cookie: user_cookie=Rg3vHJZnehYLjVg7qi3bZjzg

服务器收到请求后从请求头中拿到cookie，然后解析并到用户信息，说明此用户已登录，Cookie是将数据保存在客户端的。

这里我们可以看到，用户信息是保存在Cookie中，也就相当于是保存在浏览器中，那就说用户可以随意修改用户信息，这是一种不安全的策略！

强调一点：Cookie无论是服务器发给浏览器还是浏览器发给服务器，都是放在请求头中的！

### 三、Cookie 属性

一个Cookie有：Name、Value、Domain、Path、Expires/Max-Age、Size、HTTP、Secure、SameSite这些属性。

#### 1. Name & Value

Name表示Cookie的名称，服务器就是通过name属性来获取某个Cookie值。
Value表示Cookie 的值，大多数情况下服务器会把这个value当作一个key去缓存中查询保存的数据。

#### 2. Domain & Path

Domain表示可以访问此cookie的域名，默认为请求的地址，如网址为 www.jb51.net/test/test.aspx，那么domain默认为www.jb51.net。
这里以百度贴吧页的Cookie来讲解一下Domain属性,.baidu.com 顶级域名和.teiba.baidu.com的二级域名，所以这里就会有一个访问规则：顶级域名只能设置或访问顶级域名的Cookie，二级及以下的域名只能访问或设置自身或者顶级域名的Cookie，所以如果要在多个二级域名中（如要在.teiba.baidu.com 和 .map.baidu.com）共享Cookie的话，只能将Domain属性设置为顶级域名(.baidu.com)！

Path表示可以访问此cookie的页面路径。 比如path=/test，那么只有/test路径下的页面可以读取此cookie。
注意：Path 必须存在于请求的URL中的路径，否则浏览器将不会发送Cookie头。

#### 3. Expires/Max-Age

Expires/Max-Age表示此cookie超时时间。若设置其值为一个时间，那么当到达此时间后，此cookie失效。不设置的话默认是Session，意思是cookie会和session一起失效。当浏览器关闭(不是浏览器标签页，而是整个浏览器) 后，此cookie失效。

提示：当Cookie的过期时间被设定时，设定的日期和时间只与客户端相关，而不是服务端。
如果设置了Expires和Max-Age, Max-Age具有优先级。

#### 4. Size

Size表示Cookie的 name + value 的字符数，比如又一个Cookie：id=666，那么 Size=2+3=5 。
另外每个浏览器对Cookie的支持都不相同，即允许Cookie的个数和大小不相同。

#### 5. HTTP

HTTP表示cookie的httponly属性。若此属性为true，则只有在http请求头中会带有此cookie的信息，而不能通过document.cookie来访问此cookie。
设计该特征意在提供一个安全措施来帮助阻止通过Javascript发起的跨站脚本攻击(XSS)窃取cookie的行为

#### 6. Secure

Secure表示是否只能通过https来传递此条cookie。不像其它选项，该选项只是一个标记并且没有其它的值。

#### 7. SameSite

SameSite 可选值为 Strict、Lax 和 None。浏览器将cookies默认设置为 SameSite=Lax。如果一个需要跨域发送，那么一定要将其值设置为None。而同时N需要设置Secure属性。

### 四. SameSite 详解

#### 1. 第一方 Cookies 和 第三方 Cookies

第一方 Cookie 是由地址栏中列出的网站域设置的 Cookie，而第三方 Cookie 来自在网页上嵌入广告或图片等项的其他域来源。第一方Cookie和第三方Cookie，都是网站在客户端上存放的一小 块数据。他们都由某个域存放，只能被这个域访问。他们的区别其实并不是技术 上的区别，而是使用方式上的区别。

    比如，访问A这个网站，这个网站设置了一个Cookie，这个Cookie也只能被A这个域下的网页读取，这就是第一方Cookie。如果还是访问A这个网站，网页里有用到B网站的一张图片，浏览器在向B请求图片的时候，B设置了一个Cookie，那这个Cookie只能被B这个域访问，反而不能被A这个域访问，因为对我们来说，我们实际是在访问A这个网站被设置了一个B这个域下的Cookie，所以叫第三方Cookie。

#### 2. Cookie 的同源策略

Cookie 中的同源只关注域名，忽略协议和端口。所以

#### 参考文章

1. Cookie 的起源与发展
https://zhuanlan.zhihu.com/p/74042200

2. MDN: HTTP response header Set-Cookie
https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie