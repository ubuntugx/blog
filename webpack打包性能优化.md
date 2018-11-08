### 1. 使用 gzip 压缩打包后的 js 文件
优化浏览器下载时的文件大小（打包后的文件大小没有改变）
> webpack.config.prod.js
``` javascript
var  CompressionWebpackPlugin  =  require('compression-webpack-plugin');
// 在 pligins 中添加
new  CompressionWebpackPlugin({ //gzip 压缩
	asset:  '\[path\].gz\[query\]',
	algorithm:  'gzip',
	test:  new  RegExp(
		'\\\.(js|css)$'  //压缩 js 与 css
	),
	threshold:  10240,
	minRatio:  0.8
})
```
这样打包出的 css js，会有 css.gz js.gz，即压缩过的 js 和 css。
之后在服务器端的 nginx 配置中开启 gzip：
查看配置文件：`vim /usr/local/etc/nginx/nginx.conf`
``` javascript
// 写在 http 中就可以
gzip  on;
gzip_types application/javascript text/css image/jpeg;
```
这样浏览器下载文件时还是之前的 js，但是服务器返回的压缩过的 .gz 文件，再在本地解压，时间缩短了很多。

### 2. echart 使用外链 js 文件，不引入 npm 包
在 html 中引入 `echarts.min.js`，`china.js`，`echarts-gl.min.js`，`echarts-wordcloud.min.js`，不引入 npm 包
在 webpack生产打包配置中增加：
```javascript
externals: {
	"react":  "React",   // 左边引入时的自定义名字，右边全局变量名
	"react-dom":  "ReactDOM",
	"jquery":  "jQuery",
	"echarts":  "echarts"
}
```
https://doc.webpack-china.org/configuration/externals/#externals
打包后文件大小减小 0.5M

### 3. 配置打包去掉注释，删除debugger，删除console
``` javascript
new webpack.optimize.UglifyJsPlugin({
    comments:  false, //去掉注释
    compress: {
        warnings:  false,
        drop_debugger: true,
        drop_console: true
    }
}),
```

### 4. ant design 按需引入
https://github.com/ant-design/ant-design/issues/2656
1. 可以直接写，缺点每个组件需要单写一行
`import  Button  from  "antd/lib/button"`
2. 用对象的方式写
`import { Button } from 'antd';`
如果单用这种方式写，打包的时候每个文件会把 antd 的所有模块都加载进去，参见：
https://ant.design/docs/react/getting-started-cn#%E6%8C%89%E9%9C%80%E5%8A%A0%E8%BD%BD
需要引入 **[babel-plugin-import](https://github.com/ant-design/babel-plugin-import)** 使每个模块按需加载，并在 webpack.config.js 里配置
``` javascript
loaders: [
    ...
    {
       text: /\.(js|jsx)$/,
       include: paths.appSrc,
       loader: 'babel',
       query: {
           cacheDirectory: true,
           plugins: [["import", { libraryName: "antd", style: "css" }]]  // 这一句
       }
    },
    ...
]
```
每个引入antd的文件减小0.5M
### 5. 删除无用依赖
### 6. echart 图表懒加载
https://www.npmjs.com/package/react-lazyload
``` javascript
import LazyLoad from 'react-lazyload';
// 在需要懒加载的元素外包裹
<LazyLoad height={300} offset={10}><!--元素--></LazyLoad>
```
### 7. 使用 webpack-parallel-uglify-plugin 替代  UglifyJS 插件
https://jeffjade.com/2017/08/12/125-webpack-package-optimization-for-speed/#%E5%A2%9E%E5%BC%BA%E4%BB%A3%E7%A0%81%E4%BB%A3%E7%A0%81%E5%8E%8B%E7%BC%A9%E5%B7%A5%E5%85%B7
使用 webpack-parallel-uglify-plugin 替代 webpack.optimize.UglifyJsPlugin 并行混淆压缩 js 文件，打包时间从四分钟减到两分钟。用法和 UglifyJsPlugin 相同。
``` javascript
var ParallelUglifyPlugin=require('webpack-parallel-uglify-plugin') ;
new ParallelUglifyPlugin({
	uglifyJS: {
		output: {
			comments:  false  //去掉注释
		},
		compress: {
			warnings:  false,
			drop_debugger:  true,
			drop_console:  true
		}
	}
}),
```

### 8. happypack 多进程执行 loader
[原理](https://jeffjade.com/2017/08/12/125-webpack-package-optimization-for-speed/#%E7%94%A8-Happypack-%E6%9D%A5%E5%8A%A0%E9%80%9F%E4%BB%A3%E7%A0%81%E6%9E%84%E5%BB%BA)
[配置方式](https://github.com/amireh/happypack/issues/14#issuecomment-208254692)
引入 [happypack](https://github.com/amireh/happypack)
[支持的loader列表](https://github.com/amireh/happypack/wiki/Loader-Compatibility-List)
``` javascript
module.exports  = {
	module: {
		loaders: [
			{
				test: /.jsx?$/,
				loader:  "happypack/loader?id=happybabel",
				exclude: /node_modules/
			},
			{
				test: /\.json$/,
				exclude: /node_modules/,
				loader:  'happypack/loader?id=happyjson',
				include: [
					path.join(rootPath, "src/components"),
					path.join(rootPath, "src/mockdata"),
					path.join(rootPath, "src/views"),
				]
			},
			{
				test: /\.less$/,
				loader:  ExtractTextPlugin.extract("style", "happypack/loader?id=happyless"),
				include: [
					path.join(rootPath, "src/components/"),
					path.join(rootPath, "src/assets"),
					path.join(rootPath, "node_modules/antd"),
				]
			},
		]
	},
	plugins: [
		createHappyPlugin('happybabel', [{
			loader:  'babel-loader',
			query: {
				cacheDirectory:  true,
				plugins: [
				["import", { "libraryName":  "antd", "style":  true }] // `style: true` 会加载 less 文件
				]
			},
		}]),
		createHappyPlugin('happyjson', ['json-loader']),
		createHappyPlugin('happyless', ['css-loader?sourceMap!less']),
	]
}
function  createHappyPlugin(id, loaders) {
	return  new  HappyPack({
		id:  id,
		loaders:  loaders,
		threadPool:  happyThreadPool,
		verbose:  true
	});
}
```
打包时间减少了 15s
### 9. 检查 loader 中的 include 和 exclude 使其更加精确
https://jeffjade.com/2017/08/12/125-webpack-package-optimization-for-speed/#%E8%AE%BE%E7%BD%AE-test-amp-include-amp-exclude
``` javascript
{
	test: /\.json$/,
	exclude: /node_modules/,
	loader:  'happypack/loader?id=happyjson',
	include: [
		path.join(rootPath, "src/components"),
		path.join(rootPath, "src/mockdata"),
		path.join(rootPath, "src/views"),
	]
},
{
	test: /\.less$/,
	loader:  ExtractTextPlugin.extract("style", "happypack/loader?id=happyless"),
	include: [
		path.join(rootPath, "src/components/"),
		path.join(rootPath, "src/assets"),
		path.join(rootPath, "node_modules/antd"),
	]
},
```
搜索文件范围变窄，可以减小一点打包时间。
