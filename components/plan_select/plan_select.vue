<template>
	<view>
		<view class="plan_bcg">
			<text class="plan_text">请您订餐</text>
			<view class="plan">
				<view class="item">
					<view :class="(plan_name=='早餐')?'item_name':'item_name2'" @click="select_plan" id="早餐">
						早餐
					</view>
					<text class="item_price">￥4.00</text>
				</view>
				<view class="item">
					<view :class="(plan_name=='中餐')?'item_name':'item_name2'" @click="select_plan" id="中餐">
						中餐
					</view>
					<text class="item_price">￥7.00</text>
				</view>
				<view class="item">
					<view :class="(plan_name=='晚餐')?'item_name':'item_name2'" @click="select_plan" id="晚餐">
						晚餐
					</view>
					<text class="item_price">￥7.00</text>
				</view>
			</view>
			
			<view class="order_people">
				<text>总订餐人数：</text>
				<view class="inputbcg">
					<image :src="imgs.people"></image>
					<input @input="input_event" placeholder="0" :name="num" v-model="num" type="number" maxlength="2"/>
					<view>人</view>
				</view>
			</view>
			<view class="order_people">
				<text style="color: #007AFF;">其中清真：</text>
				<view class="inputbcg">
					<image :src="imgs.people"></image>
					<input @input="input_event2" placeholder="0" v-model="special_num" type="number" maxlength="2"/>
					<view style="color: #007AFF;">人</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				plan_name: '早餐',
				num: 0,
				breakfast_num: 0,
				lunch_num: 0,
				dinner_num: 0,
				special_num: 0,
				breakfast_special: 0,
				lunch_special: 0,
				dinner_special: 0,
				imgs:{
					'people': getApp().globalData.server_img + '/images/people.svg'
				}
			}
		},
		methods: {
			select_plan(e){
				// console.log(e.currentTarget.id)
				this.plan_name = e.currentTarget.id
				if (this.plan_name == '早餐'){
					this.num = this.breakfast_num
					this.special_num = this.breakfast_special
				}
				if (this.plan_name == '中餐'){
					this.num = this.lunch_num
					this.special_num = this.lunch_special
				}
				if (this.plan_name == '晚餐'){
					this.num = this.dinner_num
					this.special_num = this.dinner_special
				}
				
				// 发送选择的方案给父组件
				this.$emit('send_plan',this.plan_name)
			},
			input_event(e){
				// console.log(e)
				var num = e.detail.value
				if (this.plan_name == '早餐'){
					this.breakfast_num = num
				}
				if (this.plan_name == '中餐'){
					this.lunch_num = num
				}
				if (this.plan_name == '晚餐'){
					this.dinner_num = num
				}
				uni.$emit("send_dinner",[this.breakfast_num,this.lunch_num,this.dinner_num])
			},
			input_event2(e){
				// console.log(e)
				var num = e.detail.value
				if (this.plan_name == '早餐'){
					this.breakfast_special = num
				}
				if (this.plan_name == '中餐'){
					this.lunch_special = num
				}
				if (this.plan_name == '晚餐'){
					this.dinner_special = num
				}
				uni.$emit("send_special",[this.breakfast_special,this.lunch_special,this.dinner_special])
			}
		}
	}
</script>

<style>
.item{
	margin: 10rpx;
	display: flex;
	flex-direction: column;
	width: 190rpx;
	height: 165rpx;
}
.item_name{
	display: flex;
	justify-content: center;
	align-items: center;
	background-color: #2eb17f;
	border-radius: 25rpx;
	width: 190rpx;
	height: 120rpx;
	
	font-style: normal;
	font-weight: bold;
	font-size: 36rpx;
	line-height: 46rpx;
	/* identical to box height */
	
	text-align: center;
	
	color: #FFFFFF;
}
.item_name2{
	display: flex;
	justify-content: center;
	align-items: center;
	background-color: #FFFFFF;
	border-radius: 25rpx;
	width: 190rpx;
	height: 120rpx;
	
	font-style: normal;
	font-weight: bold;
	font-size: 36rpx;
	line-height: 46rpx;
	/* identical to box height */
	
	text-align: center;
	
	color: #000000;
}
.plan{
	display: flex;
}
.item_price{
	margin-top: 10rpx;
	font-style: normal;
	font-weight: bold;
	font-size: 28rpx;
	line-height: 36rpx;
	/* identical to box height */
	
	text-align: center;

	
	color: #000000;
}
.plan_bcg{
	display: flex;
	flex-direction: column;
	justify-content: center;
	align-items: center;
	background-color: #F1F4F7;
	width: 670rpx;
	height: 480rpx;
	border-radius: 20rpx;
	margin-top: 20rpx;
	padding-top: 24rpx;
}
.plan_text{
	position: relative;
	right: 230rpx;
	bottom: 20rpx;
	font-style: normal;
	font-weight: bold;
	font-size: 32rpx;
	line-height: 36rpx;
	/* identical to box height */
	
	color: #000000;
}
</style>
