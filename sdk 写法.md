### 引用 sdk 的 js 文件：
``` html
<script>
    (function(i,s,o,g,r,a,m){i['SynapseAnalyticsObject']=r;i[r]=i[r]||function(){
    (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
    m=s.getElementsByTagName(o)[0];a.src=g;a.async=1;m.parentNode.insertBefore(a,m)
    })(window,document,'script','http://static.synapse.xin/synapseweb/sdk/synapse_analytics_test.js',
    'SynapseAnalytics');
</script>
```
拆分为：
``` js
// 定义全局函数 SynapseAnalytics，将调用函数的参数 push 到 SynapseAnalytics.q 中
window['SynapseAnalytics'] = window['SynapseAnalytics'] ||
function () {
	(window['SynapseAnalytics'].q = window['SynapseAnalytics'].q || []).push(arguments);
};

// 在页面第一个 script 标签前插入 js 文件的 script 标签，异步获取文件
var a = document.createElement('script'),
m = document.getElementsByTagName('script')[0];
a.src =  'http://static.synapse.xin/synapseweb/sdk/synapse_analytics_test.js';
a.async =  1;
m.parentNode.insertBefore(a, m)
```
主要分为 定义全局函数 `SynapseAnalytics` 和 插入 sdk 文件 两部分。
全局函数 `SynapseAnalytics` 可简写为：
``` js
window.SynapseAnalytics = function(){
    window.SynapseAnalytics.q = window.SynapseAnalytics.q || [];
    // 向全局的数组中 添加当前调用 SynapseAnalytics 的参数
    window.SynapseAnalytics.q.push(arguments);
}
```
知识点：
1. `insertBefore` 调用方式`parentNode.insertBefore(newNode, referenceNode);`
`newNode` 要插入的节点
`referenceNode` 必传，传具体节点，在这个节点前插入；传 `null` 在父节点最后插入
### sdk 调用方式
``` js
// 可以在任意地方调用，不用关心 js 是否加载完成
SynapseAnalytics('create', { origin_url: 'yourOriginUrl'});
SynapseAnalytics('sendpageview');
```
### sdk 内部实现
``` js
if (window.SynapseAnalytics) {
	// 在 js 文件加载完成前，调用了 SynapseAnalytics('xxx')，传入的参数会被 push 到 SynapseAnalytics.q 里，[['create', {}], ['sendpageview']]
	var queue = window.SynapseAnalytics.q;
	if (Object.prototype.toString.call(queue) === "[object Array]") {
		for (var i =  0; i < queue.length; i++) {
			// 遍历命令二维数组，处理每一项的命令
			// 参数：命令类型 queue[i][0]，数据 queue[i][1]
			(Object.prototype.toString.call(queue[i]) ===  "[object Arguments]") &&  _handleData(queue[i][0], queue[i][1]);
		}
	}
	// 处理 sdk 先加载完时，window.SynapseAnalytics.q 为 undefined 的情况
	window.SynapseAnalytics.q = window.SynapseAnalytics.q || [];
	var arrayProto = Array.prototype;
	// 创建新对象，原型链接到 Array 原型上
	var arrayMethods = Object.create(arrayProto);
	// 重写可以直接操纵数组的方法
	var newArrProto = [];
	[
		'push',
		'pop',
		'shift',
		'unshift',
		'splice',
		'sort',
		'reverse'
	].forEach(function (method) {
		// 原生Array的原型方法
		var original = arrayMethods[method];
		// 将push，pop等方法重新封装并定义在对象newArrProto的属性上
		// 这里需要注意的是封装好的方法是定义在newArrProto的属性上而不是其原型属性
		// newArrProto.__proto__ 没有改变
		newArrProto[method] = function mutator() {
			// 这样可以监听方法触发，在方法被触发时，多做一步处理
			(Object.prototype.toString.call(arguments[0]) ===  "[object Arguments]") &&  _handleData(arguments[0][0], arguments[0][1]);
			// 调用对应的原生方法并返回结果（新数组长度）
			return original.apply(this, arguments);
		}
	})
	// 将重写后的方法赋到需要的数组原型上
	window.SynapseAnalytics.q.__proto__ = newArrProto;
}
```
重写数组原型上的方法也可以用
``` js
var arrayProto =  Object.create(Array.prototype);
['push', 'pop', 'shift', 'unshift', 'reverse', 'sort', 'splice'].forEach(function(item, index){
  arrayProto[item] = function handle(){
    console.log(arguments);
    Array.prototype[item].apply(this, arguments);
  }
})
Object.setPrototypeOf(arr, arrayProto);
arr.push(2);
arr = arr.concat(3);
console.log(arr);
```
