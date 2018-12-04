### 介绍

HTTP（超文本传输协议），规定浏览器和服务器之间的数据传输方式。

### 请求头响应头
#### 请求头
```
// 请求方法 请求url HTTP协议与版本
POST http://synapse-ai.cn/v2/aj/account/ajaxlogin HTTP/1.1 

// 发送请求的域名和端口
Host: synapse-ai.cn 

// TCP 链接不关闭 https://stackoverflow.com/questions/15460819/what-is-the-difference-between-connection-and-proxy-connection-in-http-header
Proxy-Connection: keep-alive 

// 请求的内容长度
Content-Length: 59 

// 缓存机制(http 1.0)
Pragma: no-cache 
// 缓存机制(http 1.1) https://stackoverflow.com/questions/10314174/difference-between-pragma-and-cache-control-headers
Cache-Control: no-cache 

// 客户端期望接收的数据格式
Accept: application/json, text/javascript, */*; q=0.01 

// Cookie 自动塞在请求头里
Cookie:SESSION=efdc6b24-b000-4026-8f78-e4d16b976c3d

// 跨域或者 POST 请求时会带 Origin，标识客户端的协议域名端口
Origin: http://synapse-ai.cn 

// 客户端信息
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.110 Safari/537.36 

// POST 请求时，客户端告诉服务器实际发送的数据类型
Content-Type: application/json 

// 当前网页的地址
Referer: http://synapse-ai.cn/account/login/ 

// 客户端支持的压缩算法
Accept-Encoding: gzip, deflate 

// 客户端支持的语言 q 代表权重
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8
```
#### 响应头
``` js
// HTTP 协议版本 状态码
HTTP/1.1 200 OK 

// 分块传输响应的数据
Transfer-Encoding: chunked 

// OPTION 请求中将会列出的头部信息
Access-Control-Allow-Headers: Origin,Accept,X-Requested-With,Content-Type,Access-Control-Request-Method,Access-Control-Request-Headers,Authorization 

// 可以接受的请求方法
Access-Control-Allow-Methods: POST,GET,OPTIONS,DELETE 

// OPTION 的返回结果可以被缓存多久，如果为 -1 则每次请求都需要先发 OPTION
Access-Control-Max-Age: 3600 

// 缓存机制：缓存必须在使用之前验证旧资源的状态，并且不可使用过期资源。
Cache-Control: must-revalidate 
// 缓存机制：在释放缓存副本之前，强制高速缓存将请求提交给原始服务器进行验证。
Cache-Control: no-cache 
// 缓存机制：缓存不应存储有关客户端请求或服务器响应的任何内容。
Cache-Control: no-store 

// 常见的响应的数据类型，包括一级和二级类型(/分隔)
// -   text/plain
// -   text/html
// -   text/css
// -   image/jpeg
// -   image/png
// -   image/svg+xml
// -   audio/mp4
// -   video/mp4
// -   application/javascript
// -   application/pdf
// -   application/zip
// -   application/atom+xml
// -   application/x-www-form-urlencoded（默认，普通表单提交）
// -   multipart/form-data （文件上传）
Content-Type: application/json;charset=utf-8 

// 报文创建的日期时间
Date: Mon, 03 Dec 2018 02:42:20 GMT 

// 响应过期的日期和时间，如果下次访问在时间允许的范围内，可以不用重新请求，直接访问缓存。
Expires: Thu, 01 Jan 1970 00:00:00 GMT 

// 一个 TCP 连接保持的最小时长（timeout，单位：秒）
// 和最大连接数（max），即一个 TCP 连接中允许的最大请求数，HTTP1.1 中管道连接生效（管道连接中，一次 TCP 连接中可以同时发送多个请求，服务器一一处理响应）
Keep-Alive: timeout=38 

// 缓存机制(http 1.0)
Pragma: no-cache 

// 服务器信息
Server: nginx/1.10.1 
```

### 状态码
- 2xx 成功
	- 200 OK：成功
- 3xx 重定向
	- 301 永久重定向：资源已经被永久移动到某一位置，客户端可以修改之后访问这个资源的链接
	- 302 临时重定向
	- 304 not modify 资源未被修改，使用浏览器内存缓存的文件
- 4xx 客户端错误
	- 403 Forbidden 服务器理解请求，拒绝执行
	- 404 Not Found 找不到文件，服务器普遍拒绝
	- 405 Method Not Allowed 请求方法不允许
- 5xx 服务器错误
	- 500 通用错误消息
### 跨域
参见 跨域 文档
### TCP/IP 4层协议
应用层（DNS 解析），传输层（TCP 连接，三次握手），网络层（查找 Mac 地址），网络接口层（发送请求接收数据）
### HTTP 1.1 和 2 区别
#### HTTP 1.1
- 允许在一次 TCP 连接中发送同时多个请求（管道），服务器依次响应请求，```Transfer-Encoding: chunked``` 表示响应由未定长度的数据块组成。
- 一般浏览器最多同时打开六个 TCP 连接。
- 但是因为服务器是依次响应请求的，如果有一个请求响应过慢，会有阻塞的现象。
- 解决这种问题的主要方式：减少请求数（多个js/css文件通过webpack打包为一个js，将小于5k的image转为base64添加到css文件里，图标使用iconfont），开多个 TCP 连接
#### HTTP 2
- 一个 TCP 连接里客户端和服务器都可以同时发送多个请求和响应，不按顺序。
- 所以要将请求和响应一一对应，每一个发送和接收的数据包，都有唯一的ID，客户端这边的为奇数，服务器响应的为偶数。
- 可以取消某一次请求而不关闭 TCP 连接。
- 允许服务器未经请求，主动向客户端推送资源。
### Cookie
cookie 是服务器发送给客户端的一小段信息，登录接口服务器在响应头中加上
```
Set-Cookie: SESSION=fdbcdd5e-8345-445e-8c4b-7c480042b463;Path=/;HttpOnly
```
浏览器识别这个字段，在之后发送请求头中放入 Cookie 字段

属性：
- domain：如果设置成了一级域名，下面所有的二级域名都能访问到这个 Cookie
- path：当前哪个目录可以访问到 Cookie，一般设置为根目录（都能访问）
- HttpOnly：不能通过 `document.cookie` 访问，安全性考虑

> https://juejin.im/entry/587de8341b69e600584bb947

### 浏览器缓存机制
```
// 接下来 2592000 秒可以重用这个响应
Cache-Control: max-age=2592000

// 每次都先和服务器确认资源是否变化，没变化则不下载
Cache-Control: no-catch

// 每次都请求服务器并下载
Cache-Control: no-store
```

