<template>
	<view>
<!-- 		<view style="display: flex; justify-content: center; border-radius:0rpx 0rpx 30rpx 30rpx ; filter: drop-shadow(0px 4px 4px rgba(0, 0, 0, 0.25));width: 100%; background-color: white;">
			
		</view> -->
		<!-- 导入公寓选择组件 -->
<!-- 		<Apartment_choice></Apartment_choice> -->
		
		<!-- 标题总计 -->
		<view style="display: flex; justify-content: center; margin-top: 36rpx;">
			<view style="display: flex; justify-content: space-between; width: 680rpx;">
				<text class="text_item" style="font-size: 28rpx; left: 20rpx;">我的订单</text>
				<view style="display: flex; align-items: center;">
					<text class="text_item" style="font-size: 32rpx; right: 20rpx;">总计：{{bill.length}}单</text>
				</view>		
			</view>
		</view>
		<!-- 我的订单 -->
		<view v-for="item in bill">
			<food_item :bill_time="item.bill_time" :bill_price="item.bill_price" :bill_room="item.bill_room"
						:bill_class='item.bill_class'
						:bill_lunch_num="item.bill_lunch_num"
						:bill_dinner_num="item.bill_dinner_num"
						:bill_breakfast_num="item.bill_breakfast_num"
						:breakfast_order="item.breakfast_order"
						:lunch_order="item.lunch_order"
						:dinner_order="item.dinner_order"
						:bill_apartment="item.bill_apartment"
						:breakfast_special="item.breakfast_special"
						:lunch_special="item.lunch_special"
						:dinner_special="item.dinner_special"
						:end_time='end_time'
						:start_time='start_time'
			></food_item>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// apartment: '',
				// room: '',
				bill: [
					
				],
				end_time: '',
				start_time: ''
			}
		},
		onLoad(){
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
					console.log(res.data.open_time[0])
					that.end_time = res.data.open_time[0].end_time
					that.start_time = res.data.open_time[0].start_time
				}
			})
			// this.apartment = getApp().globalData.apartment
			// this.room = getApp().globalData.room
			// this.get_roomAllbill()
			// uni.$on('send_apartment', (apartment) => {
			// 	// console.log('接受是否展示' + show)
			// 	this.apartment = apartment
			// 	// console.log(this.apartment)
			// 	this.get_roomAllbill()
			// })
			// uni.$on('send_room', (room) => {
			// 	// console.log('接受是否展示' + show)
			// 	this.room = room
			// 	// console.log(this.room)
			// 	this.get_roomAllbill()
			// })
			var that = this
			uni.request({
				url: getApp().globalData.server + '/index.php/Home/Index/find_mybill',
				data: {
					pay_user: uni.getStorageSync('openId'),
				},
				method: "POST",
				dataType: 'json',
				header: {
					'content-type': 'application/x-www-form-urlencoded' // 默认值
				},
				success(res) {
					console.log(res)
					that.bill = res.data.data
					console.log(that.bill)
				}
			})
		},
		methods: {
			// 获取用户订单
			get_roomAllbill(){
				var that = this
				uni.request({
					url: getApp().globalData.server + '/index.php/Home/Index/find_roomAllbill',
					data: {
						room: this.room,
						apartment: this.apartment
					},
					method: "POST",
					dataType: 'json',
					header: {
						'content-type': 'application/x-www-form-urlencoded' // 默认值
					},
					success(res) {
						console.log(res)
						that.bill = res.data.data
						console.log(that.bill)
					}
				})
			}
		}
	}
</script>

<style>
	page{
		background-color: #F1F4F7;
		display: flex;
		flex-direction: column;
		justify-content: center;
	}
	.text_item{
		font-style: normal;
		font-weight: 800;
		position: relative;
	}
</style>
