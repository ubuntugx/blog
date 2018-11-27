单页应用路由基于React-Router中 `browserHistory` 实现
#### 生产环境 nginx 配置
```
location / {
  root   /export/App/wecall;
  index  index.html index.htm;
  try_files $uri /index.html;
}

location /static {
  root   /export/App/wecall;
}
```
其中：
1、root：服务器根目录位置
2、index：首页文件名称
3、try_files 如果找不到一个目录的话，返回  index.html，[参考](https://stackoverflow.com/questions/17798457/how-can-i-make-this-try-files-directive-work)
4、location /static：访问静态资源，指定服务器的目录

所以，try_files 使得我访问服务器某一具体资源路径时，比如 `http://wxtest.synapse.xin/working` 或 `http://wxtest.synapse.xin/me/client/main` 服务器返回 index.html 

#### 开发环境 express 配置
``` js
app.get(/static/, (req, res) => {
	var filename = req.url.match(/\/static\/(.+)/)[1];
	res.sendFile(path.join(__dirname, './dist/static/' + filename));
});

app.get(/\/|index\.html/, (req, res) => {
	res.sendFile(path.join(__dirname, './index.html'));
});
```
知识点：
1、`browserHistory` 基于 [History API](https://developer.mozilla.org/zh-CN/docs/Web/API/History) 实现页面跳转
2、`hashHistory` 通过监听 `hashchange` 事件，结合 History API 实现路由跳转，[参考](https://segmentfault.com/a/1190000012656017)
