### new 调用函数
``` js
function Foo(name){
	this.name = name;
}
Foo.prototype.getName = function(){
	return this.name;
}
var a = new Foo();
// 获取 a 的原型，链接到了 Foo.prototype
Object.getPrototypeOf(a) === Foo.prototype;   // true
var b = new Foo('abc');
b.getName();   // abc
```
知识点：
1. 在被 new 操作符调用之前，Foo 和普通函数没有任何不同
2. 使用 new 调用函数之后，进行了四步：
	a. 创建一个新对象
	b. 将新对象的 [[Prototype]] 链接到 Foo.prototype
	c. 新对象被绑定到函数调用的 this，所以函数内部的this，指向新对象
	b. 如果 Foo 没有返回值，或者没有返回其他对象，则返回新对象。

### 避免使用 constructor
``` js
function Foo(){};
var a = new Foo();
Foo.prototype.constructor === Foo;
a.constructor === Foo;
```
Foo.prototype 默认情况下有一个属性 constructor，指向 Foo
本身，所以 a.constructor，a 本身并没有 constructor 这个属性，所以通过原型链找到 Foo.prototype 的 constructor，但是这并不可信：
```  js
function Foo(){}
Foo.prototype = {};  // 重写了 Foo.prototype
var a = new Foo();
a.constructor === Foo;  // false
a.constructor === Object;  // true
```
`Foo.prototype` 被重写后，`a.constructor` 在 `Foo.prototype `找不到 `constructor` 属性，于是沿着原型链继续向上找，找到了 `Object.prototype.constructor`。
默认的 `constructor` 属性是一个不可枚举的属性（enumerable: false），可以被修改，所以使用 `a.constructor` 是不可靠不安全的，尽量避免使用它。
