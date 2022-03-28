<template>
	<view style="background-color: white;">
		<view class="plan_time">
			<text class="plan_title">选择学院 & 选择公寓</text>
			<view class="time_content">
				<!-- 点击展示自定义弹窗 -->
				<view class="time_item" @click="show_Model">
					<text v-if="!custom_settings.settings_school">请选择学院</text>
					<text v-if="custom_settings.settings_school">{{custom_settings.settings_school}}</text>
					<image src="/static/images/arrow.svg" style="width: 25rpx; height: 25rpx;"></image>
				</view>
		
				<!-- 点击展示自定义弹窗 -->
				<view class="time_item" @click="show_Model">
					<text v-if="!custom_settings.apartment">请选择公寓</text>
					<text v-if="custom_settings.apartment">{{custom_settings.apartment}}</text>
					<image src="/static/images/arrow.svg" style="width: 25rpx; height: 25rpx;"></image>
				</view>
			</view>
		</view>
		<!-- 自定义弹窗 -->
		<view v-if="show_select">
			<select_schoolandapartment></select_schoolandapartment>
		</view>
	</view>
</template>

<script>
	export default {
		name:"select_way",
		data() {
			return {
				show_select: false,
				
				custom_settings: {
					'settings_school': '',
					'apartment': ''
				},
			};
		},
		created() {
			uni.$on('unshow', (show) => {
				// console.log('接受是否展示' + show)
				this.show_select = show
			})
			// 接受custom_value
			uni.$on('custom_value', (value) => {
				// console.log('接受是否展示' + value)
				// 将自定义方案的设置更新到本地
				this.custom_settings.apartment = value[1]
				this.custom_settings.settings_school = value[0]
				// console.log(this.custom_settings)
				// 发送school和class
				uni.$emit("send_schollandapartment",this.custom_settings)
			})
		},
		methods:{
			//展示自定义弹窗
			show_Model() {
				this.show_select = true
			},
		}
	}
</script>

<style>

.plan_time {
		width: 670rpx;
	}

	.plan_title {
		margin-left: 60rpx;
		font-style: normal;
		font-weight: 600;
		font-size: 30rpx;
		line-height: 76rpx;

		color: #0A0909;
	}

	.time_content {
		display: flex;
		justify-content: space-around;
		width: 100vw;
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
