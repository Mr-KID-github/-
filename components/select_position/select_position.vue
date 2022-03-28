<template>
	<view>
		<view class="plan_time">
			<text class="plan_title">选择学院 & 选择班级</text>
			<view class="time_content">
				<!-- 点击展示自定义弹窗 -->
				<view class="time_item" @click="show_Model">
					<text v-if="!custom_settings.settings_school">请选择学院</text>
					<text v-if="custom_settings.settings_school">{{custom_settings.settings_class}}</text>
					<image src="/static/images/arrow.svg" style="width: 25rpx; height: 25rpx;"></image>
				</view>
		
				<!-- 点击展示自定义弹窗 -->
				<view class="time_item" @click="show_Model">
					<text v-if="!custom_settings.settings_class">请选择班级</text>
					<text v-if="custom_settings.settings_class">{{custom_settings.settings_school}}</text>
					<image src="/static/images/arrow.svg" style="width: 25rpx; height: 25rpx;"></image>
				</view>
			</view>
		</view>
		<!-- 自定义弹窗 -->
		<view v-if="show_select">
			<custom_select></custom_select>
		</view>
	</view>
</template>

<script>
	export default {
		name:"select_position",
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
			uni.$on('custom_value', (value) => {
				// console.log('接受是否展示' + value)
				// 将自定义方案的设置更新到本地
				this.custom_settings.settings_school = value[1]
				this.custom_settings.settings_class = value[0]
				console.log(this.custom_settings)
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
