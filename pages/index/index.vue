<template>
	<view class="home">
		<image :src="imgs.gradient" style="position: absolute;" class="bcgimg"></image>
		<image :src="imgs.img" class="bcgimg"></image>
		<view style="display: flex; flex-direction: column; align-items: center; width: 100%;">
			<input @input="to_check" class="input_view" placeholder="输入密钥进入管理查收" placeholder-class="pla_style">
			<view class="divider_detail">——————      进入查收或订单      ——————</view>
			<view class="button_view" @click="to_order">
				<image :src="imgs.logos_go" style="width: 50rpx; height: 20rpx;"></image>
				<text>开始明日的订餐</text>
			</view>
			<view class="button_view"  @click="to_bill">
				<image :src="imgs.bill_logo" style="width: 50rpx; height: 40rpx;"></image>
				<text>查看我的订单</text>
			</view>
			<view class="button_view" @click="to_manager">
				<image :src="imgs.robot" style="width: 50rpx; height: 40rpx;"></image>
				<text>联系管理员</text>
			</view>
			<text class="gogogo">准备好干饭了吗？订餐时间：09:00 ~ 13:00</text>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				imgs:{
					'logos_go': getApp().globalData.server_img + '/images/logos_go.svg',
					'bill_logo': getApp().globalData.server_img + '/images/bill_logo.svg',
					'robot': getApp().globalData.server_img + '/images/robot.svg',
					'gradient': getApp().globalData.server_img + '/images/gradient.png',
					'img': getApp().globalData.server_img + '/images/img.png'
					
				},
				password: [
					
				],
				user_id: ''
			}
		},
		onLoad() {
			// 获取用户id
			this.user_id = uni.getStorageSync('openId')
			// 从数据库中获取密钥
			var that = this
			uni.request({
				url: getApp().globalData.server + '/index.php/Home/Index/find_pw',
				data: {

				},
				method: "POST",
				dataType: 'json',
				header: {
					'content-type': 'application/x-www-form-urlencoded' // 默认值
				},
				success(res) {
					console.log(res)
					that.password = res.data.password
				}
			})
			// 获取全部公寓学院班级信息
			uni.request({
				url: getApp().globalData.server + '/index.php/Home/Index/find_school_apartment_class',
				data: {
				
				},
				method: "POST",
				dataType: 'json',
				header: {
					'content-type': 'application/x-www-form-urlencoded' // 默认值
				},
				success(res) {
					console.log(res)
					getApp().globalData.all_apartment_school = res.data.school_apartment
					getApp().globalData.all_class_school = res.data.school_class
				}
			})
		},
		methods: {
			//获取时间函数
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
				var timer = hour;
				return timer;
			},
			to_bill(){
				// uni.showModal({
				// 	content: "将于近期开通",
				// 	showCancel:false
				// })
				if (getApp().globalData.user_id == ''){
					uni.showModal({
						title:'登录提醒',
						content:"请前往登录",
						success(res) {
							if (res.confirm){
								uni.reLaunch({
									url: '/pages/login/login'
								})
							}
						}
					})
				} else {
					uni.reLaunch({
						url: '/pages/my_bill/my_bill'
					})
				}
				
			},
			to_manager(){
				uni.showModal({
					content: "请联系本院管理员",
					showCancel:false
				})
			},
			to_check(e){
				console.log(e.detail.value)
				for (let i = 0; i < this.password.length; i++){
					var item = parseInt(this.password[i].password)
					console.log(item)
					if (e.detail.value == item){
						uni.reLaunch({
							url:'/pages/check_bill/check_bill'
						})
					}
				}
				
			},
			to_order(){
				if (getApp().globalData.user_id == ''){
					uni.showModal({
						title:'登录提醒',
						content:"请前往登录",
						success(res) {
							if (res.confirm){
								uni.reLaunch({
									url: '/pages/login/login'
								})
							}
						}
					})
				} else {
					var that = this
					uni.request({
						url: getApp().globalData.server + '/index.php/Home/Index/find_open_time',
						data: {
						
						},
						method: "POST",
						dataType: 'json',
						header: {
							'content-type': 'application/x-www-form-urlencoded' // 默认值
						},
						success(res) {
							console.log(res.data.open_time[0].end_time)
							var hour = that.getTime()
							if (hour <= res.data.open_time[0].start_time - 1) {
								uni.showModal({
									showCancel:false,
									content: "订餐时间未开始"
								})
							} else if (hour > res.data.open_time[0].end_time-1) {
								uni.showModal({
									showCancel:false,
									content: "订餐时间已结束"
								})
							} else {
								uni.redirectTo({
									url:"/pages/order/order"
								})
							}
							// uni.redirectTo({
							// 	url:"/pages/order/order"
							// })
						}
					})
				}
			},
			to_goods(){
				if (this.user_id == ''){
					uni.showModal({
						title:'登录提醒',
						content:"请前往登录",
						success(res) {
							if (res.confirm){
								uni.reLaunch({
									url: '/pages/login/login'
								})
							}
						}
					})
				} else {
					uni.redirectTo({
						url:"/pages/goods/goods"
					})
				}
			}
		}
	}
</script>

<style lang="scss">
	.gogogo{
		margin-top: 40rpx;
		margin-bottom: 40rpx;
		font-style: normal;
		font-weight: 600;
		font-size: 30rpx;
		
		color: #FFFFFF;
	}
	.button_view{
		width: 520rpx;
		margin-top: 40rpx;
		height: 85rpx;
		background-color: white;
		border-radius: 20rpx;
		display: flex;
		align-items: center;
		justify-content: center;
	}
	.button_view text{
		font-style: normal;
		font-weight: 700;
		font-size: 30rpx;
		
		color: #000000;
		
	}
	.button_view image{
		margin-left: 30rpx;
		position: absolute;
		left: 180rpx;
	}
	.divider_detail{
		margin-top: 40rpx;
		font-style: normal;
		font-weight: 700;
		font-size: 25rpx;
		text-align: center;
		
		color: #D6D6D6;
	}
	.pla_style{
		font-style: normal;
		font-weight: 500;
		font-size: 30rpx;
		
		color: #FFFFFF;
	}
	.input_view{
		border-radius: 10rpx;
		background-color: #060606;
		color: #FFFFFF;
		width: 520rpx;
		height: 85rpx;
		display: flex;
		justify-content: center;
		align-items: center;
		
		font-style: normal;
		font-weight: 500;
		font-size: 30rpx;

		color: #FFFFFF;
	}
	.bcgimg{
		height: 600rpx;
		width: 100vw;
	}
	page{
		background-color: #2EB17F;
	}
	.home{
		width: 100%;
		swiper{
			width: 750rpx;
			height: 628rpx;
			image{
				width: 100%;
				height: 100%;
			}
		}
		
		.shop{
			position: absolute;
			width: 100%;
			height: 500rpx;
			top: 670rpx;
			left: 40rpx;
			
			font-style: normal;
			font-weight: 800;
			font-size: 36rpx;
			line-height: 40rpx;
			color: #000000;
		}
		.science{
			position: relative;
			width: 100%;
			margin-top: 100rpx;
		}
		.science_active{
			position: relative;
			width: 100%;
			height: 150rpx;
			margin-top: 40rpx;
		}
	}
	
</style>
