### 介绍
react 是开源的 js 框架，主要用在单页面应用，FaceBook 2011 年出的，处理 MVVM 中的 V（视图层）的问题（M数据 GraphQL 处理，VM Redux 处理）。
### 特点
1. 虚拟 DOM：每次重新渲染，在内存中生成虚拟 DOM，和上一次生成的虚拟 DOM 对比（diff 算法，递归），将差异渲染到真实 DOM 上
2. 支持服务器端渲染（优点：可以提前渲染出 DOM 结构，优化加载速度）renderToString 方法
3. 单向数据流，数据自上而下从父组件传递给子组件，数据简单容易追踪变化（vue 通过重写 getter 和 setter ，发布订阅者实现双向数据流）
4. 页面布局组件化好复用
### 声明一个组件
``` js
// es6 模块方式引入 React(对象)
import React from 'react';

class MyComponent extends React.Component {
	constructor(props){
		this.a = '123';
		super(props);  
	}
	onClickButton(){
		console.log(this);  // undefined
		console.log('clicked!!')
	}
	render(){
		console.log(this);  // React.Component
		// react 中事件机制
		return (<div onClick={{this.onClickButton}}></div>)
	}
}

export default MyComponent
```
`onClickButton` 中 `this` 指向的 `undefined`，因为 React 在 mount 和 update 生命周期中，会解析 JSX 中的所有事件，将事件统一代理到最顶层 document 上（会在 dom 移除时移除监听函数），所以 `onClickButton` 在非严格模式下指向调用 addEventListener 的元素，严格模式指向 `undefined`。

类似 `render` 等一些其他通过实例正常调用的函数，绑定在 MyComponent 函数的原型上（见下 es5 写法），所以 this 指向 `MyComponent` 的实例。

es5
``` js
function MyComponent(){
	this.a = '123';
	React.Component.call(this);
}
// MyComponent.prototype: 
// -constructor:MyComponent(Function)
// -__proto__
MyComponent.prototype = Object.create(React.Component.prototype);
// MyComponent.prototype: __proto__:
// - constructor:React.Component
// - __proto__
// 把丢失的constructor放回来=.=，不可枚举的
Object.defineProperties(MyComponent.prototype, {
	constructor: {
		value: Bar,
	    enumerable: false,
	    writable: true,
	    configurable: true
	}
})
MyComponent.prototype.constructor = MyComponent;
MyComponent.prototype.onClickButton = function(){
	console.log('clicked!!')
}
export default MyComponent;
```
### 生命周期函数
![enter image description here](http://oi68.tinypic.com/332tnck.jpg)
在 shouldComponentUpdate 中判断 state 和 props，决定是否重新进 render。
