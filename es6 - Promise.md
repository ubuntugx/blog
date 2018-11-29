``` js
function handleTimeout(){
  var p = new Promise(function(resolve, reject){
    setTimeout(funct............................ion(){
      console.log('-- 1秒啦 --');
      // 调用 resolve 说明异步任务完成，在 .then 里可以得到数据
      resolve('hhh'); 
    }, 1000)
  })
  return p;
}
function handleTimeout2(){
  var p = new Promise(function(resolve, reject){
    setTimeout(function(){
      console.log('-- 2秒啦 --');
      // 调用 resolve 说明异步任务完成，在 .then 里可以得到数据
      resolve('hhh2'); 
    }, 2000)
  })
  return p;
}
handleTimeout().then(function(data){
  console.log('得到结果啦：', data);  // "得到结果啦：" "hhh"
});
Promise.all([handleTimeout2(), handleTimeout()]).then(function(result){
  console.log(result);  // ["hhh2", "hhh"]
})
```
