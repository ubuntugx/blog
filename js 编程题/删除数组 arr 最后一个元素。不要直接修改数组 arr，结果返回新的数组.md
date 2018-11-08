删除数组 arr 最后一个元素。不要直接修改数组 arr，结果返回新的数组
``` javascript
function truncate(arr) {
    return arr.filter(function(item, index){
        return index != arr.length -1
    })
}
```
``` javascript
function truncate(arr) {
    return arr.slice(0, arr.length-1);
}
```
``` javascript
function truncate(arr) {
	// slice 为负数，表示从倒数第几个元素开始提取
    return arr.slice(0, -1);
}
```
