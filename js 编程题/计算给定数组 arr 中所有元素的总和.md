可以有三种方法
``` javascript
function sum(arr) {
	var sumNum = 0;
	for(var i = 0; i < arr.length; i++){
		sumNum = sumNum + arr[i];
	}
	return sumNum;
}
```
``` javascript
function sum(arr) {
	return arr.reduce(function(accumulator, currentValue, currentIndex, sourceArray){
		return accumulator + currentValue;
	})
}
```
``` javascript
function sum(arr) {
	var sumNum = 0;
	arr.forEach(function(item, index){
		sumNum += item;
	})
	return sumNum;
}
```
