统计数组 arr 中值等于 item 的元素出现的次数
``` javascript
function count(arr, item) {
    return arr.reduce(function(acc, next){
        if(next === item){
            acc++;
        }
        return acc;
    }, 0)
}
```
``` javascript
function count(arr, item) {
    var res = arr.filter(function(o, i){
        return item === o;
    })
    return res.length;
}
```
