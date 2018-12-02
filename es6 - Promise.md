### 回调地狱
回调地狱不仅仅是很多回调函数嵌套带来的代码可读性的问题（倒三角形），还有最大的控制反转问题，比如说我们依赖第三方库的支付系统：
``` js
analyse.buySomething(price, function(){
	// 弹出支付成功框
})
```
在第三方支付分析工具的回调函数中，写购买的语句，调用回调函数的决定权在第三方工具手中，可能会调用多次（支付多次），提前调用，延后调用等等...我们需要在回调函数中增加很多判断，保证支付成功。
### Promise
- 我们只需要知道一个异步的任务什么时候结束，一个未来的状态，和中间执行的时间无关。
- 不需要关注foo内部如何实现，只需要监听它的完成状态
- Promise 一旦决议，就是一个不可变的值，不会因为多次查看而被修改。
- 判断一个对象是不是 Promise 的实例，判断他有没有函数类型的 then 属性（鸭子类型检测）
#### 实现两个异步加载的数，相加
``` js
var sum = function(PromiseX, PromiseY){
	// 接收 Promise 实例的数组，当两个 Promise 都执行完后，执行 .then
	// .then 中传入函数的参数也是数组，每一个 Promise resolve 后的值
	// .then 生成了一个新的 Promise 实例
  return Promise.all([PromiseX, PromiseY]).then(function(res){
    return res[0] + res[1];
  })
}
// 返回第一个异步加载数，Promise实例
var promiseX = function(){
  return new Promise(function(resolve, reject){
    resolve(2);
  })
}
// 返回第二个异步加载数，Promise实例
var promiseY = function(){
  return new Promise(function(resolve, reject){
    resolve(3);
  })
}
// sum 最后返回了一个 Promise 实例，.then 参数是return的结果？
sum(promiseX(), promiseY()).then(function(res){console.log(res)});
```
> https://codepen.io/ubuntugx/pen/ZmwBJJ
#### 成功和失败的状态捕捉
成功后的值肯定是程序手动设置的，失败的可能是手动捕捉的原因，或者因为运行异常浏览器抛出的错误。
``` js
function handleReject(){
  return new Promise(function(resolve, reject){
    var random = Math.random();
    if(random>0.5){
      resolve(1);
    }else{
      reject("too bigger");
    }
  })
}
handleReject().then(function(res){
  // 成功
  console.log(res);
}, function(err){
  // 失败,
  console.log(err);
})
```
> https://codepen.io/ubuntugx/pen/wQNobo
#### 解决控制反转的问题
我们依赖第三方库的支付系统：
``` js
analyse.buySomething(price, function(){
	// 弹出支付成功框
})
```
在第三方支付分析工具的回调函数中，写购买的语句，调用回调函数的决定权在第三方工具手中，可能会
- 调用过早/过晚
- 回调未调用
- 回调调用次数过多/过少
- 未按规则传递参数
- 吞掉错误或异常
- 是可信任的 Promise 吗
##### 调用过早/过晚
Promise 原型上的 then 方法，是异步调用的，在下一个时间点一定会被触发。
##### 回调未调用
如果 Promise 本身永远没有被决议，可以使用 *竞态* 机制，给他一个竞争者。
``` js
function timeOutPromise(delay){
  return new Promise(function(resolve, reject){
    window.setTimeout(function(){
      reject('timeout!')
    }, delay)
  })
}
function handlePromise(){
  return new Promise(function(resolve, reject){
    
  })
}
// 两个 Promise 谁先决议，后面 then 为哪个的结果
Promise.race([handlePromise(), timeOutPromise(3000)]).then(function(res){
  console.log(res);
}, function(err){
  // 可能是代码报错，foo返回拒绝，没按时完成
  // 根据 err 判断
  console.log(err);
})
```
> https://codepen.io/ubuntugx/pen/jQdKpy
##### 调用次数过少或过多
过少相当于上一节说的回调未被调用，也就是0次
过多，因为 Promise 只能被决议一次，如果调用多次 resolve 或 reject，then 也只会被调用一次，即只取第一次的回调结果。
##### 未能传递参数 / 环境值
没有传递值的话默认是 undefined。
如果 resolve 或 reject 中传递了多个参数，会只取第一个，后面的参数被忽略，所以，如果传递多个值，把他们放到数组或对象中。
##### 吞掉错误或异常
p.then 本身返回了一个新的 Promise，来接收里面代码的错误，不会影响 p 这个 Promise 决议的结果。
``` js
function promiseError(){
  return new Promise(function(resolve, reject){
    console.log(b);
    resolve(2)
  })
}

promiseError().then(function(res){
  console.log(res)
}, function(err){
  console.log(err)   // ReferenceError: b is not defined
  console.log(c)
}).then(function(res){
  console.log(res)
}, function(err){
  console.log(err)  // ReferenceError: c is not defined
})
```
> https://codepen.io/ubuntugx/pen/RqvJEX
##### 是可信任的 Promise 吗
第三方库中有一个对象拥有 then 属性（function），会把他误认为Promise，为了兼容它不是真正 Promise 的情况，使用 `Promise.resolve`，会返回一个 Promise 的实例，如果参数为 Promise 的实例，会直接返回，否则会用返回的值填充：
``` js
Promise.resolve(foo(42)).then(function(res){
	console.log(res);
}, function(err){
	console.log(err);
})
```
#### 链式 Promise
- 调用 Promise 的 then(..) 会自动创建一个新的 Promise 从调用返回。  
- 在完成或拒绝处理函数内部，如果返回一个值或抛出一个异常，新返回的（可链接的）Promise 就相应地决议。  
- 如果完成或拒绝处理函数返回一个 Promise，它将会被展开，这样一来，不管它的决议值是什么，都会成为当前 then(..) 返回的链接 Promise 的决议值。
#### 错误处理
Promise 实例上调用 then 方法，then 中的 resolve 和 reject 参数函数中如果出现错误，需要下一个 .then 中去捕获，这样像是一个无尽的过程。
``` js
Promise.resolve(foo(42)).then(function(res){
	console.log(a);  // ??? .then 返回的是 Promise，需要再 .then 去捕获
}, function(err){
	console.log(err);
})
```
Promise 原型有 .catch 方法，有人说 Promise 链的最后加上这个方法，可是 .catch 中写的错误谁来捕获呢？（也是返回了一个 Promise）
这个问题现在官方还没有完美的解决方法，在这个 asynquesce Promise 抽象库 中作者有自己的解决办法（.defer）
#### API
##### Promise.all([])
 Promise.all 参数，两个 Promise 或其他
两个 Promise 都完成后进入 .then，参数为数组，两个 Promise resolve 的值
一个 reject 就不会进入 .then
##### Promise.race([])
两个 Promise 一个完成之后进入 .then，另一个被垃圾回收
第三方 Promise 和定时器 Promise 传进来，解决没有调用回调函数的问题
##### .catch(function(){})
捕获 Promise 链中所有未被处理的错误
#### 顺序错误处理
``` js
var p = foo( 42 )  
.then( STEP2 )  
.then( STEP3 );  
```
虽然这里可能令人迷惑，但是这里的 p 并不指向链中的第一个 promise（调用  foo(42) 产生的那一个），而是指向最后一个 promise，即来自调用 then(STEP3) 的那一个。
