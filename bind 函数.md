为了修改 `this` 的指向，有几种方式（在 this 里面写了），其中有一种是硬绑定（使用 `call` 或 `apply` 绑定 `this`），因为很常用，所以封装了函数 `bind`。
``` javascript
Function.prototype.bind  =  function (oThis) {
	// this 为调用 bind 的函数，属于隐式绑定
	if (typeof this !==  "function") {
		// closest thing possible to the ECMAScript 5
		// internal IsCallable function
		throw  new  TypeError("Function.prototype.bind - what is trying to be bound is not callable");
	}
	// arguments 是只存在于函数（非箭头）中的局部变量，是一个类数组（除了 length 和 通过下标索引元素没有其他的属性），存储着函数的参数，可以把他转为一个真数组
	// call 方法第一个参数为需要改变的 this 指向，后面的参数为需要传给函数的参数（apply 也可以显示绑定 this，区别是 apply 后面的参数是一个数组）
	// slice 普通的方式调用的时候，也就是隐式调用，this 指向调用他的数组，用 call 调用的时候 this 指向传入的参数，也就是 slice 内部去处理 this 指向的值，也可以简写成 [].slice.call(arguments, 1)转化为真正的数组

	// 取出传递给新函数的参数
	var aArgs = Array.prototype.slice.call(arguments, 1),
	// this 指向调用它的函数
	fToBind = this,
	// 定义一个空函数
	fNOP = function () { },
	fBound = function () {
		return fToBind.apply(this instanceof fNOP
		? this
		: oThis || this,
		aArgs.concat(Array.prototype.slice.call(arguments)));
	}; 
	fNOP.prototype = this.prototype;
	// 新函数的prototype直接用的目标函数的prototype
	fBound.prototype = new fNOP();
	return fBound;
};
```
