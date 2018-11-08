ios 下不能自动播放音频，需要用户的动作（touchstart，click）触发
``` javascript
// 在用户触摸屏幕的时候，播放一下音频，真正需要自动播放的时候才可以播放
music.addEventListener('touchstart', function(){
	music.play();
	music.pause();
})

// 如果是微信环境的话
// 旧版(不需要引入微信的sdk)
document.addEventListener("WeixinJSBridgeReady", function () {
    music.play();
}, false);

// 新版（引入微信的sdk http://res.wx.qq.com/open/js/jweixin-1.2.0.js）
// 文档：https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421141115
// 在wx.ready里自动播放
wx.ready(function(){    
	// config信息验证后会执行ready方法，所有接口调用都必须在config接口获得结果之后，config是一个客户端的异步操作，所以如果需要在页面加载时就调用相关接口，则须把相关接口放在ready函数中调用来确保正确执行。对于用户触发时才调用的接口，则可以直接调用，不需要放在ready函数中。
	music.play();
});
```
