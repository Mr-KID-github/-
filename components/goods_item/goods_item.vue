<template>
	<view style="display: flex; width: 100vw; justify-content: center;">
		<view class="background">
			<view style="display: flex; justify-content: space-between;">
				<view class="text">定餐时间：{{bill_time}}</view>
				<view class="text" style="position: relative; left: 50rpx;">￥{{bill_price}}</view>
				<view style="display: flex;justify-content: center;align-items: center;">
					<image :src="imgs.divider" style="width: 20rpx; height: 40rpx;"></image>
					<image :src="imgs.arrow" style="width: 40rpx; height: 40rpx;"></image>
				</view>
			</view>
			<view class="detail">
				<view class="text2" style="color: #293F94;">{{bill_class}}</view>
				<image :src="imgs.divider" style="width: 20rpx; height: 40rpx;"></image>
				<view class="text2" style="color: #2EB17F;;">{{bill_num}}</view>
			</view>
		</view>
		<image :src="imgs.Notification_Check" class="Notification_img" v-if="bill_check=='1'"
			@click="change_Check"></image>
		<image :src="imgs.Notification_UnCheck" class="Notification_img" v-if="bill_check=='0'"
			@click="change_Check"></image>
	</view>
</template>

<script>
	export default {
		name:"goods_item",
		data() {
			return {
				imgs:{
					'divider': getApp().globalData.server_img + '/images/divider.svg',
					'arrow': getApp().globalData.server_img + '/images/arrow.svg',
					'Notification_Check': getApp().globalData.server_img + '/images/Notification_Check.svg',
					'Notification_UnCheck': getApp().globalData.server_img + '/images/Notification_UnCheck.svg'
				}
			};
		},
		created() {
			console.log(this.bill_class)
			console.log(this.bill_price)
			console.log(this.bill_num)
			console.log(this.bill_time)
			console.log(this.bill_check)
			console.log(this.check_time)
		},
		// check用于判断是否已确认，bill_id用来后续的修改订确认状态
		props:['bill_class','bill_price','bill_num','bill_time','bill_check','bill_id','check_time'],
		methods:{
			//获取今天日期
			getTime: function() {
				var date = new Date(),
					year = date.getFullYear(),
					month = date.getMonth() + 1,
					day = date.getDate(),
					hour = date.getHours() < 10 ? "0" + date.getHours() : date.getHours(),
					minute = date.getMinutes() < 10 ? "0" + date.getMinutes() : date.getMinutes(),
					second = date.getSeconds() < 10 ? "0" + date.getSeconds() : date.getSeconds();
				month >= 1 && month <= 9 ? (month = "0" + month) : "";
				day >= 0 && day <= 9 ? (day = "0" + day) : "";
				var timer = year + '-' + month + '-' + (day);
				return timer;
			},
			change_Check(){
				// 不可修改今天订餐状态
				if (this.bill_time == this.getTime()){
					uni.showModal({
						showCancel:false,
						content:"不可提前查收今日订餐信息"
					})
				} else {
					var that = this
					uni.showModal({
						content:"确认修改订单此时状态？",
						success(res) {
							if (res.confirm) {
								console.log('调用修改订单接口')
								var there = that
								// 根据当前状态选择调用的修改订单接口类型
								if (that.bill_check == 0) {
									uni.request({
										url: getApp().globalData.server + '/index.php/Home/Index/modify_bill2true',
										data: {
											bill_class: that.bill_class,			//传入订单班级以便统一修改
											check_time: that.check_time		//传入查收时间
										},
										method: "POST",
										dataType: 'json',
										header: {
											'content-type': 'application/x-www-form-urlencoded' // 默认值
										},
										success(res) {
											console.log(res)
											if (res.data.error_code == 0){
												there.$emit('update:bill_check',"1")
												console.log(there.bill_check)
											}
										},
										fail(res) {
											console.log("抱歉，稍后尝试！")
										}
									})
								} else if (that.bill_check == 1) {
									uni.request({
										url: getApp().globalData.server + '/index.php/Home/Index/modify_bill2false',
										data: {
											bill_class: that.bill_class,			//传入订单班级以便统一修改
											check_time: that.check_time		//传入查收时间
										},
										method: "POST",
										dataType: 'json',
										header: {
											'content-type': 'application/x-www-form-urlencoded' // 默认值
										},
										success(res) {
											console.log(res)
											if (res.data.error_code == 0){
												there.$emit('update:bill_check',"0")
												console.log(there.bill_check)
											}
										},
										fail(res) {
											console.log("抱歉，稍后尝试！")
										}
									})
									
								}
								
							}
						}
					})
				}
			}
		}
	}
</script>

<style>
	.Notification_img {
		width: 96rpx;
		height: 96rpx;
		position: absolute;
		right: 100rpx;
		margin-top: 148rpx;
	}
	.detail{
		display: flex; 
		width: 300rpx;
		justify-content: space-between;
	}
.background{
	background-color: white;
	border-radius: 20rpx;
	margin-top: 20rpx;
	display: flex;
	flex-direction: column;
	justify-content: space-between;
	width: 580rpx;
	height: 110rpx;
	padding-left: 40rpx;
	padding-right: 40rpx;
	padding-top: 30rpx;
	padding-bottom: 30rpx;
	margin-bottom: 60rpx;
}
.text{
	font-style: normal;
	font-weight: 800;
	font-size: 32rpx;
	
	color: #000000;
}
.text2{
font-style: normal;
font-weight: 800;
font-size: 28rpx;

}
</style>
