项目中用到了 react，es6，less，需要使用 webpack 的 loaders `babel-loader`，`css-loader` 等等来编译打包他们
为了实现修改的文件自动编译，热替换的功能，使用的插件有两个 `webpack-dev-middleware` 和 `webpack-hot-middleware`
### webpack-dev-middleware
我们在开发环节，每次修改完文件后，都需要先执行一次 webpack 的编译命令，等待 webpack 把文件打包到本地，然后才能进行调试，这显然是相当耗时的。虽然可以用 webpack 的 watch mode 来监听文件变化，重新打包，但这个过程会很耗时。

`webpack-dev-middleware` 的出现解决了这个问题，它会开启watch mode 来监听文件变化，并且自动在**内存中**重新快速的打包，生成新的 bundle。

所以他的优点是 自动编译（watch mode）＋速度快（走内存）
#### 文件示例
##### webpack.config.js
webpack.config.js 里的 `publicPath` 是新生成的文件指向的访问路径
``` js
module.exports = { 
	entry: './app.js', 
	output: { 
		publicPath: "/assets/", 
		filename: 'bundle.js', 
		//path: '/' //只使用 dev-middleware 的话可以忽略本属性 
	} 
};
```
如果 css 文件里这样写
``` css
section{ 
	width:300px; 
	height: 300px; 
	background-image: url(a.jpg); 	
}
```
实际访问的地址则是 `http://localhost:5000/static/a.jpg`
##### devServer.js（webpack-dev-middleware）
``` js
const path = require('path'); 
const express = require("express"); 
const app = express(); 
const webpack = require('webpack'); 
const webpackMiddleware = require("webpack-dev-middleware"); 
let webpackConf = require('./webpack.config.js');
var compiler = webpack(webpackConf);
app.use(webpackMiddleware(compiler, { 
	//使用 webpack-dev-middleware 
	publicPath: webpackConf.output.publicPath //保持和 webpack.config.js 里的 publicPath 一致 
})); 
app.get("/", function(req, res) { 
	res.render("index"); 
}); 
app.listen(3333);
```
简单来说，就是引用了 `webpack-dev-middleware` 这个组件，
``` js
var webpackMiddleware = require("webpack-dev-middleware"); 

app.use(webpackMiddleware(webpack(webpackConfig), options));
```
这里 options 中，有 publicPath 选项，用来配置打包后的文件，放到内存中哪个虚拟路径下。
所以 webpack-dev-middleware 配置的 publicPath 需要保持和 webpack.config.js 里的 publicPath 一致（并且只能是相对路径） ，才能使文件正常访问。
### webpack-hot-middleware
`webpack-dev-middleware` 虽然将内存中的文件替换了，但没有实时刷新页面，每次还需要手动刷新客户端的页面，比较麻烦。

webpack 提供了 HMR 的能力，HMR 即模块热替换（hot module replacement）的简称，它可以在应用运行的时候，不需要刷新页面，就可以直接替换、增删模块。

webpack 可以通过配置 webpack.HotModuleReplacementPlugin 插件来开启全局的 HMR 能力。

使用 `webpack-hot-middleware` 分为三步：
1. 在 `devServer.js` 里配置 `webpack-hot-middleware`
``` js
var config =  require("./build/webpack.config.dev");
var compiler =  webpack(config);
app.use(require("webpack-hot-middleware")(compiler));
```
2. webpack.config.js 文件配置 plugin
``` js
const path = require('path'); 
const webpack = require('webpack'); 
module.exports = { 
	entry: [
		'webpack-hot-middleware/client', 
		'./app.js'
	], //修改点1 
	output: { 
		publicPath: "/assets/", 
		filename: 'bundle.js' 
	}, 
	plugins: [ //修改点2 
		new webpack.HotModuleReplacementPlugin(), // 开启 HMR
		new webpack.NoEmitOnErrorsPlugin() //出错时只打印错误，但不重新加载页面 
	] 
};
```
3. app.js 在根模块允许热更新，可以先判断当前环境（`process.env.NODE_ENV ===  "production"`）
``` js
// 参数可选，对于更新的文件执行回调函数
// 灰常重要，知会 webpack 允许此模块的热更新
if (module.hot) {
	module.hot.accept("reducers/index.js", () =>
		store.replaceReducer(require("reducers/index.js").default)
	);
}
```
**HMR 提供了局部更新应用模块的能力，而不需要刷新整个应用页面**

> https://juejin.im/entry/5946137aa0bb9f006bc00565
