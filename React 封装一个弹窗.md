#### 为什么dialog的组件DOM一定要放在body根下，fixed不是无论DOM层级在哪都是根据body定位吗？
-  因为fixed元素并不总是相对于视窗进行定位的，父元素发生变换，也就是transfrom属性发生改变，如平移或旋转，会对固定定位的子元素产生影响 例子：[固定定位不固定](http://codepen.io/huangbuyi/pen/mRYXbg)。另外，以下三种情况也可能导致fixed定位的基准元素改变。
   -  `transform` 属性值不为 none 的元素
   -   `perspective` 值不为 none 的元素
   -  在 `will-change` 中指定了任意 CSS 属性
-  如果父级元素的z-index的层次比同级元素低，就算fixed的z-index比父级高，也会被父级同级元素遮挡。

参考：
http://94dreamer.com/index.php/Home/Article/index/id/65
https://www.cnblogs.com/coco1s/p/7358830.html

所以，封装弹窗的时候，弹窗需要渲染到body下。

简单的来说就是：
``` javascript
let div = document.createElement('div');
document.body.appendChild(div);
ReactDOM.render(<Modal />, div);
```
完整代码：
``` javascript
// 一个简单的弹窗实现
import React, { Component } from "react"
import ReactDOM from 'react-dom';
import "./index.less"

export default class Prompt extends Component {
	constructor(props) {
		super(props);
		this.state = {
			isModalShow: false
		}
	}
	render() {
		return (
			<div className="prompt-box">
				{this.props.text ? 
				<span style={this.props.style || null} onClick={this.onClickShowModal.bind(this)} >{this.props.text}</span> 
				: <i className="iconfont icon-i prompt-i"  style={this.props.style || null} onClick={this.onClickShowModal.bind(this)} />}
			</div>
		);
	}
	onClickShowModal() {
	    // 阻止弹窗后内容滚动
		document.documentElement.style.overflow =  'hidden';
		document.body.setAttribute("style", "overflow:hidden")
		// 在 body 上渲染弹窗
		let eleDiv = document.createElement('div');
		document.body.appendChild(eleDiv);
		eleDiv.id =  'x-modal-container'
		ReactDOM.render(<_Modal  {...this.props}  />, eleDiv);
	}
}
export class _Modal extends Component {
	closeModel() {
	    // 允许弹窗后内容滚动
		document.documentElement.style.overflow =  'auto';
		document.body.setAttribute("style", "overflow:auto");
		// 移除弹窗
		let eleDiv = document.getElementById('x-modal-container');
		document.body.removeChild(eleDiv)
	}
	render() {
		return (
			<div className="coverMap">
				<div className="shareSuc-box">
					<div className="shareSuc-container">
						<div className="shareSuc-title">{this.props.title || "说明"}</div>
						<div className="shareSuc-content">
							{this.props.content || ""}
						</div>
					</div>
					<div className="shareSuc-btn" onClick={this.closeModel.bind(this)}>关 闭</div>
				</div>
			</div>
		)
	}
}
```
``` less
.prompt-box{
	font-family: "Microsoft YaHei";
	position: relative;
	.prompt-i{
		font-size: .4rem;
		color: #cfcfcf;
		z-index: 99;
	}
	p{
		font-size: .28rem;
	}
}
#x-modal-container{
	.coverMap {
		width: 100%;
		height: 100%;
		position: fixed;
		left: 0;
		top: 0;
		background-color: rgba(0, 0, 0, .6);
		overflow-y: scroll;
		z-index: 800;
		.shareSuc-box{
			position: absolute;
			top: 50%;
			left: 50%;
			transform: translate(-50%,-50%);
			width: 100%;
			.shareSuc-container{
				width:90%;
				border-radius: .5rem;
				margin:0  auto;
				padding-top:.5rem;
				background: #fff;
				overflow: hidden;
				position: relative;
				z-index: 100;
				.shareSuc-title {
					text-align:center;
					color:#03a9f4
				}
				.shareSuc-content {
					height: 7rem;
					overflow-y: auto;
					padding: 0.3rem;
					font-size: .28rem;
					color: #000;
					white-space: initial;
				}
			}
			.shareSuc-btn {
				width: 60%;
				height: .8rem;
				line-height: .8rem;
				text-align: center;
				color: #fff;
				background: #2491FC;
				border-radius: .1rem;
				margin:0  auto;
				margin-top: .3rem;
			}
		}
	}
}
```
