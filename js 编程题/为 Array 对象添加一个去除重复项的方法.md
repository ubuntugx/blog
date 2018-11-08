为 Array 对象添加一个去除重复项的方法
输入：`[false, true, undefined, null, NaN, 0, 1, {}, {}, 'a', 'a', NaN]`
输出：`[false, true, undefined, null, NaN, 0, 1, {}, {}, 'a']`
### 方法一
``` js
Array.prototype.uniq = function () {
    var res = [];
	var flag = true;
    this.forEach(function(item){
        if(res.indexOf(item) == -1){
			if(item !== item){
				// 是 NaN
				if(flag){
					res.push(item);
					flag = false;
				}
			}else{
				res.push(item);
			}
		}
    })
    return res;
}
```
知识点：
1. `indexOf` 判断一个元素是否在数组中，使用的是 `===` 严格相等判断的
2. 使用 `===` 判断时，有两个地方需要注意，一是 `NaN` 不等于自身，二是 `+0` 和 `-0` 相等。（其余两种判断相等的方式在下面介绍）
3. 使用 `==` 或者 `===` 判断两个对象是否相等，判断的是他们引用的地址是否相同
``` js
var obj1 = {};
var obj2 = {};
obj1 === obj2;  // false
var obj3 = obj1;
obj3 === obj1;  // true
// obj3 和 obj1 引用同一个内存地址
```
4. 在和数字相关的计算失败时会出现 `NaN`，比如`Math` 的某个方法计算失败了（比如 `Math.sqrt(-1);` 取 -1 的平方根），或者将字符串解析成数字失败了（比如 `parseInt('abc', 10);`）。在自己和自己比较时，只有 `NaN` 不等于自身。
``` js
NaN === NaN;   // false
```
可以利用这个特性判断 `NaN`：
``` js
function valueIsNaN(params){
	return params !== params;
}
valueIsNaN(NaN);  // true
```
还有两种判断 `NaN` 的方法：
第一个是 `isNaN`
全局的函数 `isNaN` 会先把参数转化为数字类型，再判断，类似于
``` js
function isNaN(num){
	num = Number(num);
	return num !== num;
}
```
所以在对于空字符串和布尔型的数值，会先转为 0 或 1，所以会出现问题。
``` js
isNaN(NaN);  // true
isNaN(2);    // false
isNaN('');   // false
```
第二个是 `Number.isNaN`（推荐）
为了解决全局的 `isNaN` 的问题，`Number.isNaN` 严格判断参数是否为 `NaN`，首先是一个数字类型，然后是 `NaN`。
```js
Number.isNaN = Number.isNaN || function(value) {
    return typeof value === "number" && isNaN(value);
}
```
### 方法二
``` js
Array.prototype.uniq = function () {
	return [...new Set(this)]
}
```
知识点：
1. 展开语法 `...`，用来展开数组或对象
2. `Set` 是 es6 一种新的数据结构，和数组类似，但是它里面的成员值都是唯一的。`Set` 内部判断两个值是否不同，使用的方法类似于 `===` ，不同的是他认为 `NaN` 等于自身。`Set` 接收有 iterable 接口的数据结构（包括 [`Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Array "entries() 方法返回一个新的Array Iterator对象，该对象包含数组中每个索引的键/值对。")，[`Map`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Map "此页面仍未被本地化, 期待您的翻译!")，[`Set`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Set "Set 对象允许你存储任何类型的唯一值，无论是原始值或者是对象引用。")，[`String`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/String "此页面仍未被本地化, 期待您的翻译!")，[`TypedArray`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypedArray "一个TypedArray 对象描述一个底层的二进制数据缓存区的一个类似数组(array-like)视图。事实上，没有名为 TypedArray的全局对象，也没有一个名为的 TypedArray构造函数。相反，有许多不同的全局对象，下面会列出这些针对特定元素类型的类型化数组的构造函数。在下面的页面中，你会找到一些不管什么类型都公用的属性和方法。")，[arguments](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions_and_function_scope/arguments) 对象等等）
3. `Map` 对象保存键值对。任何值(对象或者[原始值](https://developer.mozilla.org/en-US/docs/Glossary/Primitive "原始值: A primitive (primitive value, primitive data type) is data that is not an object and has no methods. In JavaScript, there are 6 primitive data types: string, number, boolean, null, undefined, symbol (new in ECMAScript 2015).")) 都可以作为一个键或一个值。键值是有顺序的，当你遍历它时，按照插入的顺序返回。
4. 可以使用 `for...of` 遍历可迭代对象
