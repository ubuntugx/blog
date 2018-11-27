### 介绍
使用发布订阅者模式和重写getter setter，结构分为三部分：
#### 监听器
递归遍历 data 的所有属性，重写他们的 getter 和 setter
``` js
var Book = {};
Object.defineProperty(Book, 'name', {
	get: function(){return '《' + name + '》'},
	set: function(value){console.log('set', value);name=value}
})
Book.name = '111';       // 'set' 111
console.log(Book.name);  // 《111》
```

#### 订阅者
监听器在 set 函数被调用后，通过一个分发消息的函数（switch...case...）通知对应订阅者。订阅者收到数据变化的消息，更新对应视图。
#### 解析器
解析模版中的指令（`{{}}`、`v-model` 等），并初始化对应的订阅者
> https://www.cnblogs.com/canfoo/p/6891868.html
