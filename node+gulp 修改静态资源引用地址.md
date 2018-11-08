#### `gulp repl -http://static2.synapse.xin` 
##### 作用  
修改项目中所有静态资源的域名（css文件，图片，第三方插件等） 
##### 具体分析  
执行了一条名称为 repl 的 gulp 命令，具体对应的文件为`./gulpfile.js` 中 
``` javascript  
function  replacePath(arguments) {
	var args=process.argv;
	var host =  'http://static.synapse.xin';
	if (args && args.length >=  4) {
		host = args[3] ? args[3].slice(1) :  'http://static.synapse.xin';
	};
	arguments.forEach((i,k)=>{
		var input=i;
		var output=i;
		var text=fs.readFileSync(input, "utf-8");
		fs.writeFileSync(output, text.replace(/http:\/\/.+\.synapse\.xin/g, host));
	})
}
gulp.task('repl',function () {
	var fnames=['src/utils/tools.js', 'src/outside/tmpl/app.html', 'src/outside/tmpl/basic.html', 'src/outside/tmpl/index.html', 'src/outside/app.html', 'src/outside/fault_403.html',
	'src/outside/fault_404.html', 'src/outside/fault_500.html', 'src/outside/fault_mobile.html', 'src/outside/forget.html', 'src/outside/login.html', 'src/outside/register.html', 'src/outside/review.html',
	'src/outside/setpasswd.html', 'index.html', 'src/outside/active.html' ];
	replacePath(fnames);
})
```  
1、process 是 node 的一个全局对象，表示当前 node 进程的运行时信息，它可以在脚本的任意地方引用，不需要通过 require 加载

2、process.argv 返回一个数组，内容是当前进程的所有命令行参数，例如 ```  [ '/usr/local/bin/node',  '/Users/ideabinder/SYNAPSE_WEB/node_modules/.bin/gulp',  'repl',  '-http://static2.synapse.xin' ]  ```  第1个参数为 node 所在路径，第2个参数为node所执行文件的绝对路径，第3个参数开始为命令实际传入的参数。  

3、fs.readFileSync，fs.writeFileSync，使用 node 的 fs 模块读写文件，在写入文件是将文件中所有匹配的域名替换为传入域名
