synapse devServer.js 里一些路径
``` js
var path =  require("path");
var express =  require("express");
var webpack =  require("webpack");
var proxy =  require("http-proxy-middleware");
var config =  require("./build/webpack.config.dev");
var help =  require("./build/constants.js");

var app =  express();
var multiPath =  "./src/outside/";

app.get("/", function (req, res) {
	res.sendFile(path.join(__dirname, "./index.html"));
});

app.get("/account/login", function (req, res) {
	res.sendFile(path.join(__dirname, multiPath, "./login.html"));

});
app.listen(5000);
```
其中涉及到一些 node 里的路径
- `__dirname`：当前文件夹的绝对路径，比如 `/Users/ideabinder/SYNAPSE_WEB`

- `__filename`： 当前文件的绝对路径，比如 `/Users/ideabinder/SYNAPSE_WEB/devServer.js`

- `path.join()`：首先先回调用 `path.normalize` 将传入的路径格式化，然后拼接到一起，因为这个函数不会自动获取当前文件所处的路径，所以一般会搭配 `__dirname`，比如：`path.join(__dirname, 'a', 'b')`

同理可以看 webpack.config.js
``` js
output: {
	path: path.join(rootPath, "./dist/static"),
	filename: "[name].js",
	publicPath: "/static/"
}
// path: 绝对路径，打包后文件输出的目录
// publicPath: 并不会对生成文件的路径造成影响，主要是对你的页面里面引入的资源的路径做对应的补全，常见的就是css文件里面引入的图片
// [name]: 默认情况下是数字递增
```
> https://blog.csdn.net/qq_39207948/article/details/80631435
