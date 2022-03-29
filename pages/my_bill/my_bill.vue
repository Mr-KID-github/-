<template>
	<view>
		<!-- 标题总计 -->
		<view style="display: flex; justify-content: center; margin-top: 36rpx;">
			<view style="display: flex; justify-content: space-between; width: 680rpx;">
				<text class="text_item" style="font-size: 28rpx; left: 20rpx;">我的订单</text>
				<view style="display: flex; align-items: center;">
					<text class="text_item" style="font-size: 32rpx; right: 20rpx;">总计：{{}}份</text>
				</view>		
			</view>
		</view>
		<!-- 我的订单 -->
		<goods_item></goods_item>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		onLoad(){
			// 获取当前用户订单数据
			
		},
		methods: {
			
		}
	}
</script>

<style>
	.text_item{
		font-style: normal;
		font-weight: 800;
		position: relative;
	}
</style>
