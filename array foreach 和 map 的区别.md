#### map
``` javascript
var arr = [1,2,3,4,5];
var arr2 = arr.map((item, index)=>{
	return item * 2;
})
console.log(arr);  // [1, 2, 3, 4, 5]
console.log(arr2); // [2, 4, 6, 8, 10]
```
1. 会返回一个新数组
2. 不能终止循环（除非抛出异常）
3. 不对原数组进行修改
4. this 指向 window （严格模式 undefined）
#### forEach
``` javascript
var arr = [1,2,3,4,5];
var arr2 = arr.foreach((item, index)=>{
	item = item * 2;
})
console.log(arr); // [1, 2, 3, 4, 5]
console.log(arr2); // undefined
```
1. 遍历原数组做一些操作
2. 不能终止循环（除非抛出异常）
3. 不对原数组进行修改
4. this 指向 window （严格模式 undefined）
#### some
测试一些元素是否满足条件
1. `return true` 的时候终止循环
2. 不对原数组进行修改
3. this 指向 window （严格模式 undefined）
#### any
测试是否所有元素都满足条件
1. `return false` 的时候终止循环
2. 不对原数组进行修改
3. this 指向 window （严格模式 undefined）

