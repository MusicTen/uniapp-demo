<template>
	<view>
		<view v-if="camouflage_flag" class="camouflage" @tap="addlisttap(-1)"></view>
		<view @tap="addtap()" :animation="animationadd" class="add" :style="{backgroundColor:backgroundColor,color:color}">+</view>
		<view v-if="add_list_flag" :animation="animationaddlist" class="add_list">
			<view v-for="(listvalue, index) in addlistdata" :key="index" class="add_list_item" @tap="addlisttap(index)">
				<text>{{listvalue.title}}</text>
				<image :src="listvalue.src"></image>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data () {
            return {
				camouflage_flag: false, /*用于控制遮慕层显示的变量*/
				animationadd: "",
				animationaddlist: "",
				add_list_flag: false,
				defaultbackgroundcolor: null,
				defaultcolor: null,
				tapflag: true /*用于判断点击事件是否可以执行*/
            }
        },
		props:{
			backgroundColor:{ /*默认加号背景色*/
				type:String,
				default:"#ff6364"
			},
			selectbackgroundColor:{ /*选中的加号背景颜色*/
				type:String,
				default:"#FFF"
			},
			color:{ /*默认的字体颜色*/
				type:String,
				default:"#FFF"
			},
			selectcolor:{ /*选中的字体颜色*/
				type:String,
				default:"#ff6364"
			},
			addlistdata:{ /*传入数据格式:[{"title":"内容1","src":"图片的绝对路径或网址路径（不可是相对路径）"},{"title":"内容2","src":"图片的绝对路径或网址路径"}]*/
				type:Array,
				default:function(){
					return [];
				}
			}
		},
		methods:{
			addlisttap:function(index){ /*-1代表代表点击的是遮慕层*/
				if (this.tapflag) {
					this.animationadd = uni.createAnimation({
					  duration: 300
					});
					/*关闭相关*/
					this.animationadd.rotateX(0).rotate(0).step();
					this.backgroundColor = this.defaultbackgroundcolor;
					this.color=this.defaultcolor;
					
					this.animationaddlist = uni.createAnimation({
					  duration: 200
					});
					this.animationaddlist.translateY(60).step();
					
					setTimeout(function() {
						this.add_list_flag = false;
						this.camouflage_flag = false;
						this.$emit('addlisttap', index); /*将点击的序号传回父组件*/
						this.tapflag = true;
					}.bind(this), 200);
				}
				this.tapflag = false;
			},
			addtap:function () { /*悬浮的加号点击事件*/
				if (this.tapflag) {
					console.log(uni)
					this.animationadd = uni.createAnimation({ // H5暂不支持
					   duration: 300
					});
					
					if (!this.camouflage_flag) {
						this.animationadd.rotateX(180).rotate(45).step();
						this.defaultbackgroundcolor=this.backgroundColor;
						this.backgroundColor=this.selectbackgroundColor;
						this.defaultcolor=this.color;
						this.color=this.selectcolor;
						this.camouflage_flag=true;
						this.add_list_flag=true; /*显示加号点击后的内容*/
						
						/*加号点击内容的动画*/
						this.animationaddlist = uni.createAnimation({
						  duration: 50
						});
						this.animationaddlist.translateY(40).step();
						setTimeout(function() {
							this.animationaddlist = uni.createAnimation({
							  duration: 100
							});
							this.animationaddlist.translateY(-90).step();
							setTimeout(function() {
								this.animationaddlist = uni.createAnimation({
								  duration: 200
								})
								this.animationaddlist.translateY(0).step();
								setTimeout(function() {
									this.animationaddlist = uni.createAnimation({
									  duration: 100
									});
									this.animationaddlist.translateY(-15).step();
									this.tapflag = true;
								}.bind(this), 200);
							}.bind(this), 100);
						}.bind(this), 50);
					} else {
						this.animationadd.rotateX(0).rotate(0).step();
						this.backgroundColor=this.defaultbackgroundcolor;
						this.color=this.defaultcolor;
						
						this.animationaddlist = uni.createAnimation({
						  duration: 200
						});
						this.animationaddlist.translateY(60).step();
						
						setTimeout(function() {
							this.add_list_flag=false;
							this.camouflage_flag=false;
							this.tapflag = true;
						}.bind(this), 200);
					}
				}
				this.tapflag = false;
			}
		}
	}
	
</script>

<style>
	.add{ /*悬浮的加号*/
		/*基础*/
		position: fixed;/*相对于手机屏幕布局*/
		z-index: 99;/*叠层设置*/
		/*设置内部加号居中*/
		display: flex;
		align-items:center;
		justify-content:center;
		/*宽高*/
		width:45px;
		height:45px;
		line-height: 45px;
		/*位于屏幕的位置（可根据需求改）*/
		bottom: 80px;
		right:50upx;
		/*圆形（若需要正方形的加号可以去掉此样式）*/
		border-radius: 40px;
		/*四边阴影（让加号看起来有立体感）*/
		-webkit-box-shadow: #808080 0px 0px 4px;
		-moz-box-shadow: #808080 0px 0px 4px;
		box-shadow: #808080 0px 0px 4px;
		/*加号大小*/
		font-size:32px;
	}
	.add_list{
		position: fixed;
		bottom: 90px;
		right:50upx;
		z-index: 98;
		display: flex;
		flex-direction:column-reverse;
		align-items:flex-end;
		line-height: 50px;
	}
	.add_list_item{
		display: flex;
		align-items:center;
		font-size: 15px;
		padding-top: 20px;
		color: #FFFFFF;
	}
	.add_list_item image{
		margin-left: 15px;
		width: 45px;
		height:45px;
	}
	.camouflage{/*遮慕层*/
		/*基础*/
		position: fixed;/*相对于手机屏幕布局*/
		width:100%;
		height:100%;
		top:0;
		z-index: 97;/*叠层设置(（注意：值一定要比悬浮加号低）)*/
		/*遮慕背景色*/
		background-color:rgba(0,0,0,0.4);
	}
	
</style>
