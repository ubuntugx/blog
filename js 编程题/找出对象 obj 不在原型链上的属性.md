找出对象 obj 不在原型链上的属性(注意这题测试例子的冒号后面也有一个空格~)  
1、返回数组，格式为 key: value  
2、结果数组不要求顺序

``` js
function iterate(obj) {
    return Object.getOwnPropertyNames(obj).map(function(item, index){
        return item + ': ' + obj[item]
    })
}
// getOwnPropertyNames 获取所有对象自身的属性（不在原型链上的）
```
