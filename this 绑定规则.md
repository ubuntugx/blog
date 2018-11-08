`this` 的绑定规则有四种
#### 默认绑定
``` javascript
function foo(){
	console.log(this.a);
}
foo(); // undefined
```
`foo` 在 `window` 下被调用，`this` 指向 `window`（严格模式下为 `undefined`）
``` javascript
function foo(){
    'use strict';
	console.log(this.a);
}
foo(); // TypeError: Cannot read property 'a' of undefined
```
#### 隐式绑定
``` javascript
function foo(){
	console.log(this.a);
}
var obj = {
	a: 2,
	foo: foo
}
obj.foo();  // 2
```
`this` 指向直接调用它的对象
``` javascript
// 一个反例
function foo(){
	console.log(this.a);
}
var obj = {
	a: 2,
	foo: foo
}
setTimeout(obj.foo, 1000); // undefined
// setTimeout 只是通过 obj.foo 拿到了 foo 函数的引用，实际调用 foo 函数是在 window 下
```
#### 显式绑定
``` javascript
function foo() {
	console.log(this.a);
} 
var obj = {
	a: 2
}
foo.call(obj); // 2
```
硬绑定
`call` 和 `apply` 都会调用这个函数，将 `this` 绑定到第一个参数上
`bind` 会返回一个新函数，将新函数的 this 指向 `call` 到传入的第一个参数上（`bind` 的内部实现另外一篇还没写完）
#### new
``` javascript
function Foo(){
	this.aaa = 123;
}
var bar = new Foo();
console.log(bar.aaa); // 123
```
如果没有被 `new` 调用，`Foo` 就是一个普通的函数，
被 `new` 调用之后，`Foo` 被称为构造函数，执行 `new` 操作时会干下面几件事：
1. 创建一个全新的对象
2. 这个新对象会被执行 [[ 原型 ]] 连接。（后面看原型补充）
3. 这个对象被绑定到 `Foo` 调用时的 `this`
4. 如果 `Foo` 没有返回其他对象，则 `new` 调用这个函数时会返回这个新对象

正常判断 `this` 指向优先级的过程是从后往前 `new` -> 显示 -> 隐式 -> 默认

#### 绑定例外
1. 把 `null`  或 `undefined` 传入到 `call`，`apply` 或 `bind` 中时，函数会忽略 `this`，从而变成默认绑定规则
``` javascript
function foo(){
	console.log(this.a);
};
var a = 2;
foo.call(null);
```
如果不关心 this 指向的话，有两种情况会这样使用：
``` javascript
// 使用 apply 将数组展开，作为参数
function foo(a, b){
	console.log(a, b);
}
foo.apply(null, [2, 3]);  // 2 3

// 使用 bind 定义一些参数的默认值
function foo(a, b){
	console.log(a, b);
}
var bar = foo.bind(null, 2);
bar(3);  // 2 3
```
不过按照前面所说的，这种情况变成了默认绑定的规则，可能会污染全局变量，所以还是不要传 `null` 传占位的有意义的变量
``` javascript
function foo(a, b){
	console.log(a, b);
}
// 定义一个空对象，和 {} 的区别是没有链接 Object 的原型
var emptyObj = Object.create(null);
foo.apply(emptyObj, [2, 3])
```
#### 箭头函数
``` javascript
function foo(){
	setTimeout(()=>{
		console.log(this.a);
	}, 1000)
}
var obj = {
	a: 2
}
foo.call(obj);  // 2
```
箭头函数的 `this` 指向只由外层函数决定，不应用上面说的4条绑定规则，并且一旦绑定就不会被直接修改（`new` 也不行）
``` javascript
function foo(){
	return ()=>{	
		console.log(this.a);
	}
}
var obj1 = {
	a: 2
};
var obj2 = {
	a: 3
}
var bar = foo.call(obj1);  // foo 的 this 指向 2
bar.call(obj2);  // 2
```
之前没有箭头函数时，用 `var self  = this;` 或者 `bind` 来实现


