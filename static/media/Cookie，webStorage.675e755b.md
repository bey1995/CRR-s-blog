## ES6 
### 前言
写这篇文章之前，我已经了解些cookie，sessionStorage和localStorage的相关概念。

诸如，小饼干cookie，是**服务器发送到用户浏览器**其并保存在浏览器的一小段文本信息，可以用来**存储状态信息**。

用途有：会话管理（用户登录状态，购物车，游戏分数，或者其他信息），个性化设置（用户自定义设置，主题等），浏览器行为跟踪（跟踪分析用户行为等）。

它会在浏览器之后向同一服务器再次发起请求时被携带上，用于告知服务端两个请求是否来自同一浏览器。由于之后每次请求都会需要携带Cookie数据，因此会带来**额外的性能开销**。

随着现代浏览器开始支持各种各样的存储方式，Cookie 渐渐被**淘汰**。新的浏览器 API 已经允许开发者直接将数据存储到本地，如使用 Web storage API（本地存储和会话存储）或 IndexedDB。

localStorage，sessionStorage是保存在浏览器本地的一些数据，顾名思义，sessionStorage在页面关闭后存储的键值对数据消失，而localStorage除非主动清除，数据会一直存储在本地。
### Cookie
#### cookie的特点
1. 每个cookie大小不能超过4KB
2. 通过http协议来存储数据
3. cookie会影响(path设置)同一个域名下的根目录以及子目录
4. 同源政策，浏览器同源政策规定两个网站只要域名和端口相同就可以共享cookie

#### 实现方式
服务器发送的响应包含set-cookie首部字段，客户端得到响应报文后把内容保存在浏览器中。
```
HTTP/1.0 200 OK
Content-type: text/html
Set-Cookie: yummy_cookie=choco
Set-Cookie: tasty_cookie=strawberry
```
客户端之后对同一服务器发送请求时，会从浏览器提取Cookie信息并通过Cookie首部字段发送给服务器。
```
GET /sample_page.html HTTP/1.1
Host: www.example.org
Cookie: yummy_cookie=choco; tasty_cookie=strawberry

```

可以为每个cookie设置过期时间：
`Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT;`

**Set-Cookie有几个选项：**
**Expires/Max-age**:设置过期时间；
Expires为某个日期的GMT格式，Max-age为需要经过的秒数，优先级比expires高。没有设置时间，默认会话cookie，关闭浏览器就移除。
**Domain和Path**：
Path设置必须是匹配的路径和子路径才会发送cookie，domain表示指定了哪些主机可以接受cookie。若没有设置，则为当前的主机。
**Secure和HTTPOnly**：
Secure标志cookie只能通过https传输，可以防止xss攻击；
HttpOnly表示cookie无法通过JavaScript调用，防止中间人劫持；
**SameSite**：
设置SameSite=strict SameSite=Lax，则cookie不跨域发送。

第三方Cookie：对于 post，ajax 可以标示 **withCredentials 从而跨域携带 cookie**，fetch 可以设置 credentials:‘include’。

#### cookie作用域
Domain 标识指定了哪些主机可以接受 Cookie。如果不指定，默认为当前文档的主机（不包含子域名）。如果指定了 Domain，则一般包含子域名。例如，如果设置 Domain=mozilla.org，则 Cookie 也包含在子域名中（如 developer.mozilla.org）。

Path 标识指定了主机下的哪些路径可以接受 Cookie（该 URL 路径必须存在于请求 URL 中）。以字符 %x2F ("/") 作为路径分隔符，子路径也会被匹配。例如，设置 Path=/docs。

#### Js本地访问cookie
可以通过document.cookie访问本地非HttpOnly标记的cookie，也可以创建新的cookie。
```
document.cookie = "yummy_cookie=choco";
document.cookie = "tasty_cookie=strawberry";
console.log(document.cookie); //打印存储在本地的cookie
```
#### HttpOnly + Secure
`标记为 HttpOnly 的 Cookie 不能被 JavaScript 脚本调用。跨站脚本攻击 (XSS) 常常使用 JavaScript 的 document.cookie API 窃取用户的 Cookie 信息，因此使用 HttpOnly 标记可以在一定程度上避免 XSS 攻击。

`Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT; Secure; HttpOnly`

标记为 Secure 的 Cookie 只能通过被 HTTPS 协议加密过的请求发送给服务端。但即便设置了 Secure 标记，敏感信息也不应该通过 Cookie 传输，因为 Cookie 有其固有的不安全性，Secure 标记也无法提供确实的安全保障。
#### Session
除了可以将用户信息通过 Cookie 存储在用户浏览器中，也可以利用 Session 存储在服务器端，存储在服务器端的信息更加安全。

Session 可以存储在服务器上的文件、数据库或者内存中。也可以将 Session 存储在 Redis 这种内存型数据库中，效率会更高。

使用 Session 维护用户登录状态的过程如下：
1. 用户进行登录时，用户提交包含用户名和密码的表单，放入 HTTP 请求报文中；
2. 服务器验证该用户名和密码，如果正确则把用户信息存储到Redis （内存型数据库）中，它在 Redis 中的 Key 称为 Session ID；
3. 服务器返回的响应报文的 Set-Cookie首部字段包含了这个 Session ID，客户端收到响应报文之后将该 Cookie 值存入浏览器中；
4. 客户端之后对同一个服务器进行请求时会包含该 Cookie 值，服务器收到之后提取出 Session ID，从 Redis中取出用户信息，继续之前的业务操作。
应该注意 Session ID 的安全性问题，不能让它被恶意攻击者轻易获取，那么就不能产生一个容易被猜到的 Session ID 值。此外，还需要经常重新生成 Session ID。在对安全性要求极高的场景下，例如转账等操作，除了使用 Session 管理用户状态之外，还需要对用户进行重新验证，比如重新输入密码，或者使用短信验证码等方式。

#### session和cookie对比
1. Cookie 只能存储 ASCII 码字符串，而 Session 则可以存取任何类型的数据，因此在考虑数据复杂性时首选 Session；
2. Cookie 存储在浏览器中，容易被恶意查看。如果非要将一些隐私数据存在 Cookie 中，可以将 Cookie 值进行加密，然后在服务器进行解密；
3. 对于大型网站，如果用户所有的信息都存储在 Session 中，那么开销是非常大的，因此不建议将所有的用户信息都存储到 Session 中。

### sessionStorage，localStorage

sessionStorage和localStorage主要是用来作为本地存储来使用的，解决了cookie存储空间不足的问题(cookie中每条cookie的存储空间为4k)，localStorage中一般浏览器支持的是5M大小，在不同的浏览器中localStorage会有所不同。

同源政策限制。

#### 优势和局限
**localStorage的优势**
1. localStorage拓展了cookie的**4K限制**
2. localStorage会可以将**第一次请求的数据直接存储到本**地，这个相当**于一个5M大小的针对于前端页面的数据库**，相比于cookie可以节约带宽，但是这个却是只有在高版本的浏览器中才支持的。

**localStorage的局限**
1. 浏览器的大小不统一，并且在**IE8以上**的IE版本才支持localStorage这个属性
2. 目前所有的浏览器中都会把**localStorage的值类型限定为string类型**，这个在对我们日常比较常见的JSON对象类型需要一些转换
3. localStorage**在浏览器的隐私模式**下面是**不可读取**的
4. localStorage本质上是对字符串的读取，如果**存储内容多**的话会消耗内存空间，会导致**页面变卡**
5. localStorage不能被爬虫抓取到

localStorage与sessionStorage的唯一一点区别就是localStorage属于永久性存储，而sessionStorage属于当会话结束的时候，sessionStorage中的键值对会被清空。

#### 使用
在使用任何新特性时，考虑到浏览器的兼容性，都要先判断是否支持这个属性。
```
if (window.localStorage) {
}
```
可以通过localStorage.key = value的方式进行数据的键值对增加，修改。


也可用localStorage和sessionStorage的API：
1. localStorage.setItem(key, value)
2. localStorage.getItem(key)
3. localStorage.removeItem(key)
4. localStorage.clear()

#### 注意点
一般将JSON存入localStorage中，在localStorage会自动将localStorage转换成为字符串形式。

使用JSON.stringify()这个方法，来将JSON转换成为JSON字符串。

读取之后要将JSON字符串转换成为JSON对象，使用JSON.parse()方法。

### 参考文章

[LocalStorage、SessionStorage使用详解](https://blog.csdn.net/zhongzh86/article/details/55504381)

[本人CSDN的文章](https://blog.csdn.net/weixin_41835977/article/details/88958821)
