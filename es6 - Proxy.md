### 介绍
Proxy 属于元编程的一种主要方式。包装一个普通对象（纯对象，数组，函数等），定义一些基本操作的自定义行为（比如属性赋值，查找，函数调用等），这些处理函数除了把操作的结果转发到被包装的对象上，并做一些额外的操作。
### 元编程
修改代码本身的一些特性。主要特点：代码查看自身、代码修改自身、代码修改默认语言特性，以此影响其他代码。
比如说 `instanceof`，`isPrototypeOf`，`defineProperty`
### 解决的问题
es6 之前，可以通过 `defineProperty` 重写对象的 getter 和 setter。
``` js
var obj = {};
Object.defineProperty(obj, 'a', {
	get: function(){
		return '结果：' + this.value;
	},
	set: function(value){
		if(!Number.isInteger(value) || value<0 || value>200){
		 console.log('error')
		}else{
			 this.value = value*2
		}
	}
})
```
现在可以直接用 Proxy
``` js
let obj = {};
let b = new Proxy(obj, {
	set: function(obj, key, value){	
		console.log('当前对象：', obj);
		console.log('属性名：', key);
	    console.log('属性值：', value);
	    if(prop === 'age'){
	      if(!Number.isInteger(value) || value > 200 || value < 0){
	        console.log('年龄不符合规则')
	      }else{
	        obj[key] = value
	      }
	    }
	},
  get: function(obj, key){
	// 调用对象本身的对应函数，和Proxy一一对应
    return Reflect.get(obj, key);
  }
})
b.age = 100;
console.log(b, b.age)
console.log(obj.age)
b.age = 300;
console.log(b, b.age)
```
除了 `get` 和 `set` 之外，还可以重写 `defineProperty`、`call`、`apply` 等等。全部参见 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy#%E4%B8%80%E4%B8%AA%E5%AE%8C%E6%95%B4%E7%9A%84_traps_%E5%88%97%E8%A1%A8%E7%A4%BA%E4%BE%8B)

> https://codepen.io/ubuntugx/pen/NEBOzO
