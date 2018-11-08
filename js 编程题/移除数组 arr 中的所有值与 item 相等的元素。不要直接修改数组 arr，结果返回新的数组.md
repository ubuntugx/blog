``` javascript
function remove(arr, item) {
	// filter 用于过滤数组，返回通过的元素
    return arr.filter(function(o, i){
        return o != item;
    })
}
```
``` javascript
function remove(arr, item) {
	var res = [];
	arr.forEach(function(o, i){
		if(o != item){
			res.push(o)
		}
	})
	return res;
}
```
