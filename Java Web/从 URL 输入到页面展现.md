# 从 URL 输入到页面展现到底发生什么？

![img](C:\Users\52739\Documents\从URL输入到页面展现)

总体来说分为以下几个过程:

- DNS 解析:将域名解析成 IP 地址
- TCP 连接：TCP 三次握手
- 发送 HTTP 请求
- 服务器处理请求并返回 HTTP 报文
- 浏览器解析渲染页面
- 断开连接：TCP 四次挥手

## **一、URL **

URL（Uniform Resource Locator），统一资源定位符，用于定位互联网上资源，俗称网址。

遵守以下的语法规则：

```js
scheme://host.domain:port/path/filename
```

- scheme - 定义因特网服务的类型。常见的协议有 http、https、ftp、file，其中最常见的类型是 http，而 https 则是进行加密的网络传输。
- host - 定义域主机（http 的默认主机是 www）
- domain - 定义因特网**域名**，比如 [http://w3school.com.cn](http://w3school.com.cn/)
- port - 定义主机上的端口号（http 的默认端口号是 80）
- path - 定义服务器上的路径（如果省略，则文档必须位于网站的根目录中）。
- filename - 定义文档/资源的名称

http://www.w3school.com.cn/html/index.asp

## **二、域名解析（DNS）**

解析域名对应的IP地址

### **1. IP 地址**

IP 地址是指互联网协议地址，是 IP Address 的缩写。

IP 地址是一个 32 位的二进制数，比如 127.0.0.1 为本机 IP

### 2.域名解析

DNS 协议提供通过域名查找 IP 地址，或逆向从 IP 地址反查域名的服务

DNS 是一个网络服务器，我们的域名解析简单来说就是在 DNS 上记录一条信息记录

```text
例如 baidu.com  220.114.23.56（服务器外网IP地址）80（服务器端口号）
```

### 3.浏览器如何通过域名去查询URL对应的IP

- 浏览器缓存：浏览器会按照一定的频率缓存 DNS 记录。

  

- 操作系统缓存：如果浏览器缓存中找不到需要的 DNS 记录，那就去操作系统中找。hosts文件

  

- 路由缓存：路由器也有 DNS 缓存。

  

- ISP 的 DNS 服务器：ISP 是互联网服务提供商(Internet Service Provider)的简称，ISP 有专门的 DNS 服务器应对 DNS 查询请求。114.114.114 电信

  

- 根服务器：ISP 的 DNS 服务器还找不到的话，它就会向根服务器发出请求，进行递归查询（DNS 服务器先问根域名服务器.com 域名服务器的 IP 地址，然后再问.baidu 域名服务器，依次类推）

### 4.总结

浏览器通过向 DNS 服务器发送域名，DNS 服务器查询到与域名相对应的 IP 地址，然后返回给浏览器，浏览器再将 IP 地址打在协议上，同时请求参数也会在协议搭载，然后一并发送给对应的服务器。

## **三、TCP 三次握手**

在客户端发送数据之前会发起 TCP 三次握手用以同步客户端和服务端的序列号和确认号，并交换 TCP 窗口大小信息。

![img](C:\Users\52739\Documents\三次握手)

## **1. TCP 三次握手的过程如下：**

- **客户端发送一个带 SYN同步 ( **SYN**chronize ) 序号=1，Seq=X 的数据包到服务器端口**（第一次握手，由浏览器发起，告诉服务器我要发送请求了）
- **服务器发回一个带 SYN=1， ACK=X+1， Seq=Y 的响应包以示传达确认信息**（第二次握手，由服务器发起，告诉浏览器我准备接受了，你赶紧发送吧）
- **客户端再回传一个带 ACK=Y+1， Seq=Z 的数据包，代表“握手结束”**（第三次握手，由浏览器发送，告诉服务器，我马上就发了，准备接受吧）

## **2. 为何需要三次握手**

- 为了实现可靠数据传输， TCP 协议的通信双方， 都必须维护一个序列号， 以标识发送出去的数据包中， 哪些是已经被对方收到的。 三次握手的过程即是通信双方相互告知序列号起始值， 并确认对方已经收到了序列号起始值的必经步骤
- 如果只是两次握手， 至多只有连接发起方的起始序列号能被确认， 另一方选择的序列号则得不到确认

**注意：** HTTP是基于TCP协议的，所以每次都是客户端发送请求，服务器应答，但是TCP还可以给其他应用层提供服务，即可能A、B在建立链接之后，谁都可能先开始通信。　

　　如果两次，那么B无法确定B的信息A是否能收到，所以如果B先说话，可能后面的A都收不到，会出现问题 。

　　如果四次，那么就造成了浪费，因为在三次结束之后，就已经可以保证A可以给B发信息，A可以收到B的信息； B可以给A发信息，B可以收到A的信息。

## 3.四次握手

![å¨è¿éæå¥å¾çæè¿°](C:\Users\52739\Documents\四次握手过程)

 TCP 与 UDP 的一个基本区别， TCP 是可靠通信协议， 而 UDP 是不可靠通信协议。

- TCP 的可靠性含义： 接收方收到的数据是完整， 有序， 无差错的。
- UDP 不可靠性含义： 接收方接收到的数据可能存在部分丢失， 顺序也不一定能保证。

TCP 协议为了实现可靠传输， 通信双方需要判断自己已经发送的数据包是否都被接收方收到， 如果没收到， 就需要重发。 为了实现这个需求， 很自然地就会引出**序号（sequence number）** 和 **确认号（acknowledgement number）** 的使用。

发送方在发送数据包（假设大小为 10 byte）时， 同时送上一个序号 (假设为 500)，那么接收方收到这个数据包以后， 就可以回复一个确认号（510 = 500 + 10） 告诉发送方 “我已经收到了你的数据包， 你可以发送下一个数据包， 序号从 510 开始” 。

## **四、发送 HTTP 请求**

TCP 三次握手结束后，开始发送 HTTP 请求报文。

请求报文由请求行（request line）、请求头（header）、请求体(body)三个部分组成

![img](C:\Users\52739\Documents\HTTP请求报文)

### **1. 请求行包含请求方法、URL、协议版本**

- 请求方法包含 8 种：GET、POST、PUT、DELETE、PATCH、HEAD、OPTIONS、TRACE。
- URL 即请求地址，由 <协议>：//<主机>：<端口>/<路径>?<参数> 组成
- 协议版本即 http 版本号

```text
POST /chapter17/user.html HTTP/1.1
```

以上代码中“POST”代表请求方法，“/chapter17/user.html”表示 URL，“HTTP/1.1”代表协议和协议的版本。现在比较流行的是 Http1.1 版本

### **2. 请求头包含请求的附加信息，由关键字/值对组成，每行一对，关键字和值用英文冒号“:”分隔。**

请求头部通知服务器有关于客户端请求的信息。它包含许多有关的客户端环境和请求正文的有用信息。其中比如：**Host，表示主机名，虚拟主机；Connection,HTTP/1.1 增加的，使用 keepalive，即持久连接，一个连接可以发多个请求；User-Agent，请求发出者，兼容性以及定制化需求。**

### **3. 请求体，可以承载多个请求参数的数据，包含回车符、换行符和请求数据，并不是所有请求都具有请求数据。**

```text
name=tom&password=1234&realName=tomson
```

上面代码，承载着 name、password、realName 三个请求参数。

## **五、服务器处理请求并返回 HTTP 报文**

### **1. 服务器**

服务器是网络环境中的高性能计算机，它侦听网络上的其他计算机（客户机）提交的服务请求，并提供相应的服务，比如网页服务、文件下载服务、邮件服务、视频服务。

而客户端主要的功能是浏览网页、看视频、听音乐等等，两者截然不同。 

每台服务器上都会安装处理请求的应用——web server。

常见的 web server 产品有 apache、nginx、IIS 或 Lighttpd 等。 

**web server 担任管控的角色**，对于不同用户发送的请求，会结合配置文件，把不同请求委托给服务器上处理相应请求的程序进行处理

（例如 CGI 脚本，JSP 脚本，servlets，ASP 脚本，服务器端 JavaScript，或者一些其它的服务器端技术等），

然后返回后台程序处理产生的结果作为响应。

## **2. MVC 后台处理阶段**

后台开发现在有很多框架，但大部分都还是按照 MVC 设计模式进行搭建的。

MVC 是一个设计模式，将应用程序分成三个核心部件：

模型（model）-- 视图（view）--控制器（controller），

它们各自处理自己的任务，实现输入、处理和输出的分离。

![img](C:\Users\52739\Documents\MVC处理)

> 1、视图（view）

**它是提供给用户的操作界面，是程序的外壳。**

> 2、模型（model）

**模型主要负责数据交互。**在 MVC 的三个部件中，模型拥有最多的处理任务。一个模型能为多个视图提供数据。

> 3、控制器（controller）

**它负责根据用户从"视图层"输入的指令，选取"模型层"中的数据，然后对其进行相应的操作，产生最终结果。**控制器属于管理者角色，从视图接收请求并决定调用哪个模型构件去处理请求，然后再确定用哪个视图来显示模型处理返回的数据。

---

首先浏览器发送过来的请求先经过**控制器**，控制器进行逻辑处理和请求分发，

接着会调用模型，这一阶段**模型**会获取 redis db 以及 MySQL 的数据，

获取数据后将渲染好的页面，响应信息会以响应报文的形式返回给客户端，

最后浏览器通过渲染引擎将网页呈现在用户面前。

## **3. http 响应报文**

响应报文由响应行（request line）、响应头部（header）、响应主体三个部分组成。如下图所示：

![img](C:\Users\52739\Documents\HTTP相应报文)

(1) 响应行包含：协议版本，状态码，状态码描述

状态码规则如下：

- 1xx：指示信息--表示请求已接收，继续处理。
- 2xx：成功--表示请求已被成功接收、理解、接受。
- 3xx：重定向--要完成请求必须进行更进一步的操作。
- 4xx：客户端错误--请求有语法错误或请求无法实现。
- 5xx：服务器端错误--服务器未能实现合法的请求。

(2) 响应头部包含响应报文的附加信息，由 名/值 对组成

![1562723921625](C:\Users\52739\Documents\ResponseHeaders)

(3) 响应主体包含回车符、换行符和响应返回数据，并不是所有响应报文都有响应数据

## **六、浏览器解析渲染页面**

浏览器拿到响应文本 HTML 后，接下来介绍下浏览器渲染机制

![img](C:\Users\52739\Documents\浏览器渲染机制)

浏览器解析渲染页面分为一下五个步骤：

- 根据 HTML 解析出 DOM 树
- 根据 CSS 解析生成 CSS 规则树
- 结合 DOM 树和 CSS 规则树，生成渲染树
- 根据渲染树计算每一个节点的信息
- 根据计算好的信息绘制页面

## **七、断开连接**

**当数据传送完毕，需要断开 tcp 连接，此时发起 tcp 四次挥手**。

![img](C:\Users\52739\Documents\断开连接)

- **发起方向被动方发送报文，Fin、Ack、Seq，表示已经没有数据传输了。并进入 FIN_WAIT_1 状态**。(第一次挥手：由浏览器发起的，发送给服务器，我请求报文发送完了，你准备关闭吧)
- **被动方发送报文，Ack、Seq，表示同意关闭请求。此时主机发起方进入 FIN_WAIT_2 状态**。(第二次挥手：由服务器发起的，告诉浏览器，我请求报文接受完了，我准备关闭了，你也准备吧)
- **被动方向发起方发送报文段，Fin、Ack、Seq，请求关闭连接。并进入 LAST_ACK 状态**。(第三次挥手：由服务器发起，告诉浏览器，我响应报文发送完了，你准备关闭吧)
- **发起方向被动方发送报文段，Ack、Seq。然后进入等待 TIME_WAIT 状态。被动方收到发起方的报文段以后关闭连接。发起方等待一定时间未收到回复，则正常关闭**。(第四次挥手：由浏览器发起，告诉服务器，我响应报文接受完了，我准备关闭了，你也准备吧)