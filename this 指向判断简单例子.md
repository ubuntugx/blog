#### 一个 this 指向的例子
``` javascript
var obj = {  
	count: 0,  
	cool: function coolFn() {  
		if (this.count < 1) {  
			setTimeout( function timer(){  
				this.count++;  
				console.log( " awesome?",this.count );  
			}, 100 );  
		}  
	}  
};
obj.cool();
```
`setTimeout` 是在全局作用域下调用的，所以 `this` 指向 `window`（严格模式 `undefined`），想要让 `this` 指向 `obj` 对象，有以下几种方法：
``` javascript
var obj = {  
	count: 0,  
	cool: function coolFn() {
		var self = this;   // 声明一个变量把 this 存起来
		if (this.count < 1) {  
			setTimeout( function timer(){  
				self.count++;  
				console.log( " awesome?",self.count );  
			}, 100 );  
		}  
	}  
};
obj.cool();
```
``` javascript
var obj = {  
	count: 0,  
	cool: function coolFn() {
		if (this.count < 1) {  
			// 使用箭头函数，继承了父函数的 this 绑定
			// 缺点：箭头函数是匿名函数
			setTimeout(() => {  
				this.count++;  
				console.log( " awesome?",this.count );  
			}, 100 );  
		}  
	}  
};
obj.cool();
```
``` javascript
var obj = {  
	count: 0,  
	cool: function coolFn() {
		if (this.count < 1) {  
			setTimeout(function timer() {  
				this.count++;  
				console.log( " awesome?",this.count );  
			}.bind(this), 100 );  
		}  
	}  
};
obj.cool();
```
