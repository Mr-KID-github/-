<template>
	<view class="">
		<!-- 导入方案选择组件 -->
		<plan_select @send_plan="get_plan"></plan_select>
<!-- 		<view class="order_people">
			<text>订餐人数：</text>
			<view class="inputbcg">
				<image src="/static/images/people.svg"></image>
				<input />
				<view>人</view>
			</view>
		</view> -->
		<!-- 导入学院选择组件 -->
		<select_position></select_position>
		<!-- 导入公寓选择组件 -->
		<Apartment_choice></Apartment_choice>
		
		<view class="Total">
			<text
				class="Total_price">Total:￥{{bill.price}}</text>
			<view>
				<text class="Total_extend">早餐:￥{{4*bill.breakfast_num}}</text>
				<text class="Total_extend">午餐:￥{{7*bill.lunch_num}}</text>
				<text class="Total_extend">晚餐:￥{{7*bill.dinner_num}}</text>
			</view>
			
		</view>
		
		<view class="button">
			<image src="/static/images/REVIEW_MENU.svg"></image>
			<image src="/static/images/CHECK_OUT.svg" @click="check_out"></image>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				plan_name:'', 		// 早餐、中餐、晚餐
				bill: {
					'breakfast_num': 0,
					"lunch_num": 0,
					"dinner_num": 0,
					"school": "",
					"class": "",
					"apartment": "",
					"room": "",
					"price": 0
				}			// 订单
			}
		},
		onLoad() {
			// 接受早中晚订餐人数
			uni.$on('send_dinner', (value) => {
				// console.log('接受早中晚订餐人数' + value)
				// 将自定义方案的设置更新到本地
				this.bill.breakfast_num = value[0]
				this.bill.lunch_num = value[1]
				this.bill.dinner_num = value[2]
				// 计算价格
				this.bill.price = 4*this.bill.breakfast_num + 7 * this.bill.lunch_num + 7 * this.bill.dinner_num
				console.log(this.bill)
			})
			// 接受学院班级
			uni.$on('send_schollandclass',(value) => {
				console.log('接受学院班级' + value)
				this.bill.school = value.settings_school
				this.bill.class = value.settings_class
				console.log(this.bill)
			})
			// 接受公寓
			uni.$on("send_apartment",(value) => {
				console.log('接受公寓' + value)
				this.bill.apartment = value
				console.log(this.bill)
			})
			// 接受寝室号
			uni.$on("send_room",(value) => {
				console.log('接受寝室号' + value)
				this.bill.room = value
				console.log(this.bill)
			})
		},
		methods: {
			// 接受从子组件方案选择器里传入的方案
			get_plan(plan_name) {
				console.log(plan_name)
				this.plan_name = plan_name
			},
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
				var timer = year + '-' + month + '-' + day + ' ' + hour + ':' + minute + ':' + second;
				return timer;
			},
			// 确认方案
			check_out(){
				// 检验有没有填好信息
				if (this.bill.breakfast_num == 0 && this.bill.lunch_num == 0 && this.bill.dinner_num == 0){
					uni.showModal({
						title: "友情提示",
						showCancel: false,
						content:"哥们，你们也没有订餐呀？"
					})
				} else {
					if (this.bill.school!='' && this.bill.apartment!='' && this.bill.room!=''){
						if (this.bill.breakfast_num != 0 && this.bill.lunch_num != 0 && this.bill.dinner_num != 0){
							console.log("全部订餐啦！")
							console.log(this.bill)
							// 调用方案接口
							uni.request({
								url: getApp().globalData.server + '/index.php/Home/Index/send_bill',
								data: {
									school: this.bill.school,
									apartment: this.bill.apartment,
									class: this.bill.class,
									room: this.bill.room,
									breakfast_num: this.bill.breakfast_num,
									lunch_num: this.bill.lunch_num,
									dinner_num:this.bill.dinner_num,
									price: this.bill.price,
									time: this.getTime()	//获得当前时间
								},
								method: "POST",
								dataType: 'json',
								header: {
									'content-type': 'application/x-www-form-urlencoded' // 默认值
								},
								success: function (res) {  //后端返回的数据
									console.log(res)
								},
								fail(res) {
									
								}
							})
						} else{
							if (this.bill.breakfast_num == 0){
								uni.showModal({
									title: "友情提示",
									content:"你们寝室确定不吃早饭吗？",
									success (res) {
										if (res.confirm) {
										  console.log('用户点击确定')
										  console.log(this.bill)
										}
									}
								})
							}
							if (this.bill.lunch_num == 0){
								uni.showModal({
									title: "友情提示",
									content:"你们寝室确定不吃中饭吗？",
									success (res) {
										if (res.confirm) {
										  console.log('用户点击确定')
										  console.log(this.bill)
										}
									}
								})
							}
							if (this.bill.dinner_num == 0){
								uni.showModal({
									title: "友情提示",
									content:"你们寝室确定不吃晚饭吗？",
									success (res) {
										if (res.confirm) {
										  console.log('用户点击确定')
										  console.log(this.bill)
										}
									}
								})
							}
						}
					} else {
						if (this.bill.school=='') {
							uni.showModal({
								showCancel: false,
								content:"请选择学院班级"
							})
						} else if (this.bill.apartment=='') {
							uni.showModal({
								showCancel: false,
								content:"请选择公寓"
							})
						} else if (this.bill.room=='') {
							uni.showModal({
								showCancel: false,
								content:"请填写寝室号"
							})
						}
					}
					
				}
				
			}
		}
	}
</script>

<style>
	.Total_price {
		font-style: normal;
		font-weight: bold;
		font-size: 50rpx;
		line-height: 60rpx;
	
		color: #000000;
	}
	
	.Total {
		margin-top: 40rpx;
		display: flex;
		flex-direction: column;
	}
	
	.Total_extend {
		margin-top: 10rpx;
		font-style: normal;
		margin-right: 20rpx;
		font-weight: 700;
		font-size: 28rpx;
		color: #000000;
	}
	
	.button {
		display: flex;
		justify-content: center;
		align-items: center;
		margin-top: 40rpx;
		margin-bottom: 50rpx;
	}
	
	.button image {
		width: 330rpx;
		height: 100rpx;
		margin-right: 10rpx;
	}
page{
	display: flex;
	flex-direction: column;
	align-items: center;
}
.order_people{
	display: flex;
	margin-top: 30rpx;
	position: relative;
	right: 100rpx;
}
.order_people text{
	font-style: normal;
	font-weight: 600;
	font-size: 30rpx;
	line-height: 76rpx;

	color: #0A0909;
}
.inputbcg{
	display: flex;
	justify-content: space-around;
	width: 200rpx;
	height: 80rpx;
	background-color: white;
	border-radius: 20rpx;
	align-items: center;
}
.inputbcg image{
	width: 40rpx;
	height: 40rpx;
}
.inputbcg view{
font-style: normal;
font-weight: 700;
font-size: 15px;
line-height: 38px;
/* identical to box height, or 253% */

display: flex;
align-items: center;

color: #0A0909;
}

.inputbcg input{
	max-width: 35rpx;
	font-style: normal;
	font-weight: 700;
	font-size: 30rpx;
	color: #0A0909;
}
</style>
