在数组 arr 末尾添加元素 item。不要直接修改数组 arr，结果返回新的数组
``` javascript
function append(arr, item) {
	// 可以用 slice 复制一个数组，默认从0开始，截取到数组最后一位
    var resArr = arr.slice();
    // push 会修改原数组，返回修改后的数组长度
    resArr.push(item);
    return resArr;
}
```
``` javascript
function append(arr, item) {
	// concat 可以将两个数组拼接，也可以将值和数组拼接
    return arr.concat(item);
}
```
