[http-proxy-middleware](https://github.com/chimurai/http-proxy-middleware) 是一个基于 node 内置的 [http-proxy](https://github.com/nodejitsu/node-http-proxy)

用法
``` javascript
var express = require('express');
var proxy = require('http-proxy-middleware');

var app = express();

// 将 /api 的请求代理到 http://www.example.org
app.use('/api', proxy({target: 'http://www.example.org', changeOrigin: true}));
app.listen(3000);
```
其中如果域名配置了Name-based，需要将  `changeOrigin` 设置为 `true`
>网址名称对应（Name-based）是借由辨识客户端所以提供的网址，决定其所对应的服务，这个方法有效的减少IP地址的占用，但缺点是必须仰赖[DNS名称对应服务](https://zh.wikipedia.org/wiki/DNS "DNS")的支持，若名称对应服务中断，对应此名称的服务也会无法取用。

