<template>
	<view>
		<view class="background">
			<view class="item_bar" @click="to_breakfast">
				<text :class="select=='breakfast'?'select_item':'unselect_item'">早餐-查收</text>
				<view class="select_background" v-if="select=='breakfast'"></view>
			</view>
			<image class="divider" src="/static/images/divider.svg"></image>
			<view class="item_bar" @click="to_lunch">
				<text :class="select=='lunch'?'select_item':'unselect_item'">中餐-查收</text>
				<view class="select_background" v-if="select=='lunch'"></view>
			</view>
			<image class="divider" src="/static/images/divider.svg"></image>
			<view class="item_bar" @click="to_dinner">
				<text :class="select=='dinner'?'select_item':'unselect_item'">晚餐-查收</text>
				<view class="select_background" v-if="select=='dinner'"></view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		name:"select_time",
		data() {
			return {
				select: 'breakfast',
			};
		},
		// props:['func'],
		created() {
			var that = this
			// uni.$on('send_type',(custom_type)=>{
			// 	console.log('接受到定制服务'  + custom_type)
			// 	if (custom_type=='breakfast'){
			// 		that.to_breakfast()
			// 	}
			// 	if (custom_type=='lunch'){
			// 		that.to_lunch()
			// 	}
			// 	if (custom_type=='dinner'){
			// 		that.to_dinner()
			// 	}
			// })
		},
		methods: {
			to_breakfast(){
				this.select = 'breakfast'
				// 发送信息的页面
				// $emit(eventName, data)  
				uni.$emit('select_bar',this.select)
			},
			to_lunch(){
				this.select = 'lunch'
				// 发送信息的页面
				// $emit(eventName, data)  
				uni.$emit('select_bar',this.select);
			},
			to_dinner(){
				this.select = 'dinner'
				// 发送信息的页面
				// $emit(eventName, data)  
				uni.$emit('select_bar',this.select);
			}
		}
	}
</script>

<style>
.item_bar{
	position: relative;
	text-align: center;
	display: flex;
	align-items: center;
	justify-content: center;
	width: 160rpx;
}
.divider{
	width: 20rpx;
	height: 60rpx;
	margin-top: 20rpx;
}
.background{
	background-color: white;
	height: 100rpx;
	width: 100%;
	display: flex;
	justify-content: space-around;
}
.select_item{
	font-style: normal;
	font-weight: 900;
	font-size: 13.799px;
	line-height: 19px;
	/* identical to box height */
	
	text-align: center;
	letter-spacing: 0.197129px;
	
	color: #3C3C3C;
}
.select_background{
	position: absolute;
	bottom: 0rpx;

	width: 160rpx;
	height: 16rpx;
	
	background: #293F94;
	border-radius: 40rpx 40rpx 0 0;
	box-shadow: 8rpx 0rpx 8rpx rgba(43, 45, 55, 0.01);
}
.unselect_item{
	font-style: normal;
	font-weight: 800;
	font-size: 13.799px;
	line-height: 18px;
	/* identical to box height, or 129% */
	
	text-align: center;
	letter-spacing: 0.197129px;
	
	color: #A4B6C0;
}
</style>
