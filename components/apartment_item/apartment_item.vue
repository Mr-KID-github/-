<template>
	<view>
		<!-- 自定义选择器 -->
		<!-- 遮罩层 -->
		<view class="mask"  catchtouchmove='ture'></view> 
		
		<view class="Model">
			<view class="pickerBtn">
				<view class="cancer" @click="cancer">取消</view>
				<text class="item">选择您的配送安排</text>
				<view class="confirm" @click="confirm">确定</view>
			</view>
			<picker-view class="picker-view" @change="bindchange">
				<picker-view-column>
					<view class="item" v-for="(item,index) in apartmentList" :key="index">{{item}}</view>
				</picker-view-column>
			</picker-view>
		</view>
	</view>
</template>

<script>
	export default {
		name:"apartment_item",
		data() {
			return {
				apartmentList:[],
				showmask: true,
				apartment: 0,	// 选择公寓的下标
			};
		},
		props:['show'],
		created() {
			this.schools = getApp().globalData.all_apartment_school
			for(let key in this.schools){
					// console.log(key) //打印每个key
					console.log(this.schools[key]) // 打印每个Key对应的值
					for (let i=0; i < this.schools[key].length; i++){
						console.log(this.schools[key][i])
						if (this.apartmentList.indexOf(this.schools[key][i]) == -1) {
							this.apartmentList.push(this.schools[key][i])
						}
					}
			}
			console.log(this.apartmentList)
		},
		methods:{
			bindchange(e){
				this.apartment = e.detail.value[0]
				console.log(e)
			},
			picker(e){
				this.mask = !this.mask 
			},
			cancer(){
				this.showmask = false
				uni.$emit('unshow',this.showmask)
				uni.$emit('apartment_value',['请选择公寓'])
			},
			confirm(){
				// setTimeout( () => {
				//     // 这里添加您的逻辑
					
				// }, 200)
				// console.log(this.schools[this.school].name)
				// console.log(this.schools[this.school].data[this.class])
				this.showmask = false
				uni.$emit('unshow',this.showmask)
				uni.$emit('apartment_value',[this.apartmentList[this.apartment]])
			}
		}
	}
</script>

<style>
	.Model{
		width: 100%;
		position: fixed;
		bottom: 0rpx;
	}
	.pickerBtn {
		width: 680rpx;
		height: 101rpx;
		line-height: 101rpx;
		background-color: #fff;
		font-size: 40rpx;
		display: flex;
		justify-content: space-between;
		position: absolute;
		z-index: 1;
		border-radius: 40rpx 40rpx 0rpx 0rpx;
	}
	.cancer {
		color: #0076FF;
		padding-left: 40rpx;
		box-sizing: border-box;
		font-style: normal;
		font-weight: 600;
		font-size: 30rpx;
		line-height: 75rpx;
	}
	.confirm {
		color: #FE4533;
		padding-right: 40rpx;
		box-sizing: border-box;
		font-style: normal;
		font-weight: 600;
		font-size: 30rpx;
		line-height: 75rpx;
	}
	.picker-view {
	        width: 680rpx;
			height: 300rpx;
			margin-top: 60rpx;
			text-align: center;
			background-color: #f8f8f6;
	}
	.item{
		font-style: normal;
		font-weight: 600;
		font-size: 30rpx;
		line-height: 75rpx;
		
		color: #0A0909;
	}
	.select_title{
		font-style: normal;
		font-weight: 600;
		font-size: 30rpx;
		line-height: 75rpx;
		margin-right: 42rpx;
		
		color: #0A0909;
		
		background-color: white;
		width: 100%;
		z-index: 0;
		text-align: center;
	}
	.mask {
		position:absolute;
		top:0;
		left:0;
		margin-top: 0%;
		width: 100%;
		height:127vh;
	
		z-index: 0;
		background: rgba(0,0,0,0.5);
	}
	
</style>
