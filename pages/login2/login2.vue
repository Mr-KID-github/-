<template>
	<view>
		<view>
		      <view class='header'>
		            <image src='/static/img/logo.png'></image>
		        </view>
		
		        <view class='content'>
		            <view>申请获取以下权限</view>
		            <text>获得您的手机号用作保存信息的身份验证</text>
		        </view>
		  <button class="bottom" open-type='getPhoneNumber' @click="getPhoneNumber">点击手机号授权授权</button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		onLoad() {
		  wx.hideHomeButton()
		},
		methods: {
			
			  getPhoneNumber (e) {
			    // console.log(e)
			    //调出前面微信授权界面储存起来的openID
			    var openId = wx.getStorageSync('openId')
			    var session_key = wx.getStorageSync('session_key')
			    // console.log(openId)
			    wx.request({
			      url: "https://qiubao.ltd/球宝/index.php/Home/QiuBao/getPhoneNumber",
			      header: {"Content-Type": "application/x-www-form-urlencoded" },
			      method: "post",
			      data: {
			        encryptedData: e.detail.encryptedData, iv: e.detail.iv, session_key:session_key, openId:openId
			      },
			      success:function(res){
			        console.log(res);
			        if (res.data.msg.phoneNumber){
			          wx.setStorageSync('phoneNumber', res.data.msg.phoneNumber);
			          setTimeout(function(){
			            wx.reLaunch({
			              url: '/pages/index/index',
			            })
			          },200);
			        }else{
			          wx.showToast({
			            title: '授权失败',
			            icon:'loading'
			          })
			        }
			      },
			      fail:function(){
			        wx.showToast({
			          title: '授权失败',
			          icon: 'loading'
			        })
			      }
			    })
			  }
		}
	}
</script>

<style>
/* 开始 */
page {
  height: 100%;
  display: table;
}
.header {
    margin: 90rpx 0 90rpx 50rpx;
    border-bottom: 1px solid #ccc;
    text-align: center;
    width: 650rpx;
    height: 300rpx;
    line-height: 450rpx;
}

.header image {
    width: 200rpx;
    height: 200rpx;
}

.content {
    margin-left: 50rpx;
    margin-bottom: 90rpx;
}

.content text {
    display: block;
    color: #9d9d9d;
    margin-top: 40rpx;
}

.bottom {
    border-radius: 70rpx;
    margin: 70rpx 50rpx;
    font-size: 35rpx;
    background:rgb(36, 22, 165);
    color: #fff;
}

</style>
