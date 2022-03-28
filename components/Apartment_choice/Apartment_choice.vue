<template>
	<view>
		<view class="plan_time">
			<text class="plan_title">选择公寓 & 输入寝室号</text>
			<view class="time_content">
				<!-- 点击展示自定义弹窗 -->
				<view class="time_item" @click="show_Model">
					<text v-if="!custom_settings.apartment">请选择公寓</text>
					<text v-if="custom_settings.apartment">{{custom_settings.apartment}}</text>
					<image src="/static/images/arrow.svg" style="width: 25rpx; height: 25rpx;"></image>
				</view>
		
				<view class="order_apartment">
					<text>寝室号：</text>
					<view class="inputbcg2">
						<input @input="input_event"/>
					</view>
				</view>
			</view>
		</view>
		<!-- 自定义弹窗 -->
		<view v-if="show_select">
			<apartment_item></apartment_item>
		</view>
	</view>
</template>

<script>
	export default {
		name:"Apartment_choice",
		data() {
			return {
				show_select: false,
				
				custom_settings: {
					
				},
			};
		},
		created() {
			uni.$on('unshow', (show) => {
				// console.log('接受是否展示' + show)
				this.show_select = show
			})
			uni.$on('apartment_value', (value) => {
				// console.log('接受是否展示' + value)
				// 将自定义方案的设置更新到本地
				this.custom_settings.apartment = value[0]
				// console.log(this.custom_settings)
				uni.$emit("send_apartment",this.custom_settings.apartment)
			})
		},
		methods:{
			//展示自定义弹窗
			show_Model() {
				this.show_select = true
			},
			input_event(e){
				uni.$emit("send_room",e.detail.value)
			}
		}
	}
</script>

<style>
	.order_apartment{
		display: flex;
		margin-top: 30rpx;

	}
	.order_apartment text{
		font-style: normal;
		font-weight: 600;
		font-size: 30rpx;
		line-height: 76rpx;
	
		color: #0A0909;
	}
.inputbcg2{
	display: flex;
	justify-content: space-around;
	width: 200rpx;
	height: 80rpx;
	background-color: #f8f8f6;
	border-radius: 20rpx;
	align-items: center;
}
.inputbcg2 input{
	max-width: 70rpx;
	font-style: normal;
	font-weight: 700;
	font-size: 30rpx;
	color: #0A0909;
}
.plan_time {
	margin-top: 20rpx;
		width: 670rpx;
	}

	.plan_title {
		font-style: normal;
		font-weight: 600;
		font-size: 30rpx;
		line-height: 76rpx;

		color: #0A0909;
	}

	.time_content {
		display: flex;
		justify-content: space-between;
		align-items: center;
	}

	.time_item {
		width: 320rpx;
		height: 95rpx;
		background-color: #f8f8f6;
		border-radius: 20rpx;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.time_item image {
		width: 42rpx;
		height: 42rpx;
		margin-left: 30rpx;
		margin-right: 30rpx;
	}

	.time_item text {
		font-style: normal;
		font-weight: 600;
		font-size: 30rpx;
		line-height: 75rpx;
		margin-right: 42rpx;

		color: #0A0909;
	}

	.time_item view {
		font-style: normal;
		font-weight: 600;
		font-size: 28rpx;
		margin-right: 18rpx;

		color: #0A0909;
	}

</style>