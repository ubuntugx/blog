### 画一条虚线
业务中画一条有一个弯的虚线，直接用一个div，设置边框解决的。
``` css
.dash-line {
	width: 100%;
	height: 21px;
	border-left: 1px dashed #fff;
	border-bottom: 1px dashed #fff;
	border-bottom-left-radius: 10px;
}
```
### 画一个三角形

### table 内部内容过长超出外层 div
https://blog.csdn.net/zzq900503/article/details/48733747
``` html
<html>
	<head>
		<style type="text/css">
			div{
				width: 100%;
				border: 1px solid #F00;
				height: 20px;
			}
			table.one
			{
				table-layout: automatic
			}
			table.two
			{
				table-layout: fixed
			}
			table.two td{
				overflow: hidden;
				text-overflow: ellipsis;
			}
		</style>
	</head>
	<body>
		<div>
			<table class="one" border="1" width="100%">
				<tr>
					<td width="20%">1000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000</td>
					<td width="40%">10000000</td>
					<td width="40%">100</td>
				</tr>
			</table>
		</div>
		<br />
		<div>
			<table class="two" border="1" width="100%">
				<tr>
					<td width="20%">1000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000</td>
					<td width="40%">10000000</td>
					<td width="40%">100</td>
				</tr>
			</table>
		</div>
	</body>
</html>
```
在 ```table``` 定义样式，``` table-layout: fixed``` 列的宽度由表格和设定的列宽来确定。
内部的 ```td```，设置 ``` overflow: hidden; text-overflow: ellipsis; ``` 隐藏超出的内容。
http://www.w3school.com.cn/cssref/pr_tab_table-layout.asp

### flex 布局中 justify-content: space-between 的换行问题
https://segmentfault.com/q/1010000005880045/a-1020000005995909

第一行的三个元素两端对齐，第二行的两个元素也两端对齐了，可以希望让它左对齐。
``` html
<div class="container">
	<span>1</span>
	<span>2</span>
	<span>3</span>
	<span>4</span>
	<span>5</span>
</div>
<style>
.container {
	width: 400px;
	display:flex;
	justify-content: space-between;
	flex-flow: row wrap;
}
</style>
```
解决方法
``` css
.container:after{
    content:'';
    flex: auto;
}
```
在父级元素后加一个伪元素 `:after`（在指定元素后面插入内容），其中 `content` 是要插入的内容，还可以写一些其他的样式。`flex: auto` 填充剩余的位置。
### css3怎么实现高度从固定到自动的过渡动画
https://www.zhihu.com/question/35991373/answer/130256417

`transition` 会忽略 `height: auto` ，有两种方法实现：
1. 动态计算元素高度 https://www.zhangxinxu.com/wordpress/2015/01/content-loading-height-change-css3-transition-better-experience/
2. 设置最大高变化
http://jsfiddle.net/w1mw829k/15/
