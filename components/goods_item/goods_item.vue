<template>
	<view>
		<view style="display: flex; width: 100vw; justify-content: center;">
			<view class="background" @click="show_detail">
				<view style="display: flex; justify-content: space-between;">
					<view class="text">定餐时间：{{bill_time}}</view>
					<view class="text" style="position: relative; left: 50rpx;">￥{{bill_price}}</view>
					<view style="display: flex;justify-content: center;align-items: center;">
						<image :src="imgs.divider" style="width: 20rpx; height: 40rpx;"></image>
						<image :src="imgs.arrow" v-if="!show_room" style="width: 40rpx; height: 40rpx; transform: rotate(90deg);"></image>
						<image :src="imgs.arrow" v-if="show_room" style="width: 40rpx; height: 40rpx;"></image>
					</view>
				</view>
				<view class="detail'">
					<view class="text2" style="color: #293F94;">{{bill_class}}</view>
					<image :src="imgs.divider" style="width: 20rpx; height: 40rpx; margin-right: 20rpx;margin-left: 20rpx;"></image>
					<view class="text2" style="color: #2EB17F;">{{bill_num}}</view>
					<view class="text2"style="color: #00ACD7; margin-left: 20rpx;"  v-if="special_num!='0'">清真：{{special_num}}份</view>
				</view>
				
			</view>
			<image :src="imgs.Notification_Check" class="Notification_img" v-if="bill_check=='1'"
				@click="change_Check"></image>
			<image :src="imgs.Notification_UnCheck" class="Notification_img" v-if="bill_check=='0'"
				@click="change_Check"></image>
			
		</view>
		<view class="room_detail" v-if="show_room" >
			<view v-for="item in room_detail" style="display: flex; justify-content: space-between; margin-top: 5rpx; margin-bottom: 5rpx;">
				<view class="text2" style="color: #293F94;">{{item.bill_room}}寝室</view>
				<image :src="imgs.arrow_up" class="arrow_up"></image>
				<view v-if="check_time=='breakfast'">
					<view class="text2" style="color: #2EB17F;">早饭：{{item.bill_breakfast_num}}份</view>
					<view v-if="item.breakfast_special!='0' "class="text2"style="color: #00ACD7;">({{item.breakfast_special}}份清真)</view>
				</view>
				<view v-if="check_time=='lunch'">
					<view class="text2" style="color: #2EB17F;">午饭：{{item.bill_lunch_num}}份</view>
					<view  v-if="item.lunch_special!='0'" class="text2"style="color: #00ACD7;">({{item.lunch_special}}份清真)</view>
				</view>
				<view v-if="check_time=='dinner'">
					<view class="text2" style="color: #2EB17F;">晚饭：{{item.bill_dinner_num}}份</view>
					<view  v-if="item.dinner_special!='0'"class="text2"style="color: #00ACD7;">({{item.dinner_special}}份清真)</view>
				</view>
			</view>
		</view>
		<view :class="show_room?'note2':'note1'">
			<view style="width: 90rpx;" class="note_title">备注：</view>
			<input v-model="note_detail" @input="note" class="note_content" placeholder="无" placeholder-style="color:#979797"/>
		</view>
	</view>
</template>

<script>
	export default {
		name:"goods_item",
		data() {
			return {
				room_detail: [],
				note_detail: '',	// 备注信息
				show_room:false,
				imgs:{
					'divider': getApp().globalData.server_img + '/images/divider.svg',
					'arrow': getApp().globalData.server_img + '/images/arrow.svg',
					'Notification_Check': getApp().globalData.server_img + '/images/Notification_Check.svg',
					'Notification_UnCheck': getApp().globalData.server_img + '/images/Notification_UnCheck.svg',
					'arrow_up': getApp().globalData.server_img + '/images/arrow_up.svg'
				}
			};
		},
		created() {
			// console.log(this.bill_class)	//订单班级
			// console.log(this.bill_price)	//订单总价
			// console.log(this.bill_num)		//订单总数
			// console.log(this.bill_time)		//订单时间
			// console.log(this.bill_check)	//是否确认
			// console.log(this.check_time)	//查收时间（早饭or午饭or晚饭）
			// console.log(this.special_num)	//清真餐
			// 调用find_note接口
			var that = this
			uni.request({
				url: getApp().globalData.server + '/index.php/Home/Index/find_note',
				data: {
					bill_class: this.bill_class,			//传入订单班级以便统一修改
					check_time: this.check_time,			//传入查收时间
					bill_time: this.bill_time,				//传入订单时间
				},
				method: "POST",
				dataType: 'json',
				header: {
					'content-type': 'application/x-www-form-urlencoded' // 默认值
				},
				success(res) {
					// console.log(res.data.note[0].note_detail)
					if (res.data.error_code == 0){
						that.$nextTick(function () {that.note_detail  = res.data.note[0].note_detail})
					}
				}
			})
		},
		// check用于判断是否已确认，bill_id用来后续的修改订确认状态
		props:['bill_class','bill_price','bill_num','bill_time','bill_check','bill_id','check_time','special_num'],
		methods:{
			// 备注信息
			note:function(e){
				// console.log(e)
				this.note_detail = e.detail.value
				// 调用修改备注接口
				setTimeout(()=>{
					this.modify_node()
				},2000)		// 延时调用接口，等待用户输入
				
			},
			// 点击展示详情信息
			show_detail:function(e){
				this.show_room = !this.show_room
				// 查询该班级当天所有订单信息
				var that = this
				uni.request({
					url: getApp().globalData.server + '/index.php/Home/Index/find_classbill',
					data: {
						// 查询条件（不直接查询所有，而是输入条件在数据库中查询，因为数据量一大用数据库的搜索算法更快，不然还得自己写算法）
						time: this.bill_time,
						class: this.bill_class
					},
					method: "POST",
					dataType: 'json',
					header: {
						'content-type': 'application/x-www-form-urlencoded' // 默认值
					},
					success(res) {
						console.log(res)
						that.room_detail = res.data.data
					}
				})
			},
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
			// 修改订餐备注
			modify_node(){
				var that = this
				console.log("开始修改订餐备注")
				// console.log(this.note_detail)
				if (!this.note_detail){
					this.note_detail = '无'
				}
				uni.request({
					url: getApp().globalData.server + '/index.php/Home/Index/modify_note',
					data: {
						bill_class: this.bill_class,			//传入订单班级以便统一修改
						check_time: this.check_time,			//传入查收时间
						bill_time: this.bill_time,				//传入订单时间
						note_detail: this.note_detail			//备注内容
					},
					method: "POST",
					dataType: 'json',
					header: {
						'content-type': 'application/x-www-form-urlencoded' // 默认值
					},
					success(res) {
						console.log(res)
					}
				})
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
					
					// 1、将订单表中该订单此时间段状态修改；
					uni.showModal({
						content:"确认修改订单状态？",
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
											check_time: that.check_time,			//传入查收时间段
											bill_time: that.bill_time				//传入订单时间
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
											check_time: that.check_time,			//传入查收时间段
											bill_time: that.bill_time				//传入订单时间
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
	.note_title{
		font-style: normal;
		font-weight: 700;
		font-size: 28rpx;
		color: #000000;
	}
	.note_content{
		font-style: normal;
		font-weight: 500;
		font-size: 25rpx;
		
		color: #000000;
	}
	.note2{
		display: flex;
		align-items: center;
		position: relative;
		bottom: 25rpx;
		margin-left: 60rpx;
		padding-left: 20rpx;
		padding-right: 20rpx;
		width: 600rpx;
		min-height: 80rpx;
		background-color: white;
		border-radius: 20rpx;
	}
	.note1{
		display: flex;
		align-items: center;
		position: relative;
		bottom: 40rpx;
		margin-left: 60rpx;
		padding-left: 20rpx;
		padding-right: 20rpx;
		width: 450rpx;
		min-height: 80rpx;
		background-color: white;
		border-radius: 20rpx;
	}
	.arrow_up{
		width: 45rpx;
		height: 45rpx;
	}
	.room_detail{
		width: 300rpx;
		background-color: white;
		background-color: white;
		position: relative;
		bottom: 40rpx;
		border-radius: 20rpx;
		margin-left: 60rpx;
		padding-left: 50rpx;
		padding-right: 50rpx;
		padding-top: 25rpx;
		padding-bottom: 25rpx;
	}
	.Notification_img {
		width: 96rpx;
		height: 96rpx;
		position: absolute;
		right: 100rpx;
		margin-top: 148rpx;
	}
	.detail{
		display: flex; 
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
