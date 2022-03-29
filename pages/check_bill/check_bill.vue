<template>
	<view>
		<!-- 选择条件 -->
		<select_way></select_way>
		<!-- 选择时间 -->
		<select_time></select_time>
		<!-- 标题总计 -->
		<view style="display: flex; justify-content: center; margin-top: 36rpx;">
			<view style="display: flex; justify-content: space-between; width: 680rpx;">
				<text class="text_item" style="font-size: 28rpx; left: 20rpx;">订单列表</text>
				<view style="display: flex; align-items: center;">
					<text class="text_item" style="font-size: 32rpx; right: 20rpx;">总计：{{check_time=='breakfast'?breakfast_sum:(check_time=='lunch'?lunch_sum:dinner_sum)}}份</text>
				</view>		
			</view>
		</view>
		<view v-for="item in bill">
			<!-- 订单列表 -->
			<goods_item v-if="check_time=='breakfast'" :check_time="check_time" :bill_id="item.bill_id" :bill_check.sync="item.breakfast_order" :bill_time="send_time" :bill_class="item.bill_class" :bill_price="item.bill_breakfast_num*4" :bill_num="'早饭:  '+item.bill_breakfast_num+' 份'"></goods_item>
			<goods_item v-if="check_time=='lunch'" :check_time="check_time" :bill_id="item.bill_id" :bill_check.sync="item.lunch_order" :bill_time="send_time" :sum="lunch_sum" :bill_class="item.bill_class" :bill_price="item.bill_lunch_num*7" :bill_num="'午饭:  '+item.bill_lunch_num+' 份'"></goods_item>
			<goods_item v-if="check_time=='dinner'" :check_time="check_time" :bill_id="item.bill_id" :bill_check.sync="item.dinner_order" :bill_time="send_time" :sum="dinner_sum" :bill_class="item.bill_class" :bill_price="item.bill_dinner_num*7" :bill_num="'晚饭:  '+item.bill_dinner_num+' 份'"></goods_item>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				bill: {
					
				},
				check_time: 'breakfast'	,//查收时间
				send_time: this.sendTime(),	//送餐时间
				breakfast_sum: 0,					// 早餐总数
				lunch_sum: 0,					// 午餐总数
				dinner_sum: 0,					// 晚餐总数
			}
		},
		onLoad() {
			// this.send_time = this.sendTime()
			uni.$on('select_bar',(check_time) => {
				this.check_time = check_time
				console.log(this.check_time)
			})
			uni.$on('send_schollandapartment', (value) => {
				console.log(value)
				// 看是统计今日订餐还是查收昨日订单
				if (value[1]){
					// 查收昨日订单
					var time = this.getTime()
					this.send_time = time
				} else {
					// 统计今日订餐信息
					var time = this.sendTime()
					this.send_time = time
				}
				this.getbill(time,value[0])
			})
			
		},
		methods: {
			// 调用接口获取订单信息
			getbill:function(date,value){
				var that = this
				// 选择好条件后进行后台查询获取订单信息
				uni.request({
					url: getApp().globalData.server + '/index.php/Home/Index/find_bill',
					data: {
						// 查询条件（不直接查询所有，而是输入条件在数据库中查询，因为数据量一大用数据库的搜索算法更快，不然还得自己写算法）
						time: date,
						school: value.settings_school,
						apartment: value.apartment
					},
					method: "POST",
					dataType: 'json',
					header: {
						'content-type': 'application/x-www-form-urlencoded' // 默认值
					},
					success(res) {
						console.log(res)
						// 查询成功
						if (res.data.error_code == 0){
							that.bill = res.data.data
							// 初始化数量
							that.breakfast_sum = 0
							that.lunch_sum = 0
							that.dinner_sum = 0
							for (let i=0; i < that.bill.length; i++){
								var item = that.bill[i]
								// console.log(parseInt(item.bill_breakfast_num))
								that.breakfast_sum += parseInt(item.bill_breakfast_num)
								that.lunch_sum += parseInt(item.bill_lunch_num)
								that.dinner_sum += parseInt(item.bill_dinner_num)
							}
							// console.log(that.bill)
						} else if (res.data.error_code == 2){
							that.bill = []
						}
					},
					fail(res) {
						console.log(res)
						that.bill = []
						console.log(that.bill)
					}
				})
							
			},
			//昨日订单查收
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
				var timer = year + '-' + month + '-' + (day-1);
				return timer;
			},
			//今日订单统计
			sendTime: function() {
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
			}
		}
	}
</script>

<style>
	.text_item{
		font-style: normal;
		font-weight: 800;
		position: relative;
	}
page{
	background-color: #F1F4F7;
}
</style>
