<template>
	<view>
		<view>
		      <view class='header'>
		            <image src='/static/img/logo.png'></image>
		            <view style="padding-bottom: 20rpx;">防疫订餐小程序
		            </view>
		        </view>
		
		        <view class='content'>
		            <view>申请获取以下权限</view>
		            <text>获得你的公开信息(昵称，头像等)</text>
		        </view> 
		        <view class="container">
		  <view class="userinfo">
		    <block v-if="!hasUserInfo">
		      <button v-if="canIUseGetUserProfile" @click="getUserProfile" style="background-color:#00B800; color: white;"> 微信授权登录 </button>
		      <button v-else open-type="getUserInfo" @click="getUserInfo" style="background-color:#00B800; color: white;"> 微信授权登录 </button>
		    </block>
		  </view>
		</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				    hasUserInfo: false,
				    canIUseGetUserProfile: false,
			}
		},
		onLoad() {
		  uni.hideHomeButton()
		  if (uni.getUserProfile) {
		    this.canIUseGetUserProfile = true
		  }
		},
		methods: {
			  
			  getUserProfile(e) {
			    // 推荐使用uni.getUserProfile获取用户信息，开发者每次通过该接口获取用户个人信息均需用户确认
			    // 开发者妥善保管用户快速填写的头像昵称，避免重复弹窗
			    uni.getUserProfile({
			      desc: '用于个人资料', // 声明获取用户个人信息后的用途，后续会展示在弹窗中，请谨慎填写
			      success: (res) => {
			        uni.login({
			          success: (res) => {
			              // 通过code换取openid
			              if (res.code) {
			                  console.log(res.code)
			                  uni.request({
			                      url: "https://qiubao.ltd/球宝/index.php/Home/QiuBao/getOpenid",
			                      method: "post",
			                      data: {
			                        code: res.code,
									program: '1'
			                      },
			                      header: {
			                        'content-type': "application/x-www-form-urlencoded"   //注意，这里得用这个header！！
			                      },
			                      success: (res) => {    
			                        console.log(res)     
			                        console.log('获取到的用户openid为：' + res.data.openid);
			                        if (res.data && res.data.openid) {
			                            // 获取的openid存入storage，方便之后使用
			                            uni.setStorageSync("openId", res.data.openid);
										getApp().globalData.user_id = res.data.openid
										// 存入数据库中
										uni.request({
											url: getApp().globalData.server + '/index.php/Home/Index/senduser',
											method: "post",
											data: {
											    open_id: uni.getStorageSync('openId'),
											},
											header: {
											  'content-type': "application/x-www-form-urlencoded"   //注意，这里得用这个header！！
											},
											success(e) {
												console.log(e)
											}
										})
			                        }
			                        if (res.data && res.data.session_key) {
			                          // 获取的session_key存入storage，方便之后使用
			                          uni.setStorageSync("session_key", res.data.session_key);
			                          console.log('获取到的用户session_key为：' + res.data.session_key);
			                      }
			                      },
			                  });
			              }
			          },
			          fail: () => {},
			          complete: () => {
			            getApp().globalData.userInfo = res.userInfo
			            uni.setStorageSync("userInfo", res.userInfo);
			            // console.log(getApp().globalData.userInfo)
			            this.hasUserInfo = true
			            uni.redirectTo({
			              url: '/pages/index/index',
			            })
			          },
			        })
			      }
			    })
			  },
			  getUserInfo(e) {
			    // 不推荐使用getUserInfo获取用户信息，预计自2021年4月13日起，getUserInfo将不再弹出弹窗，并直接返回匿名的用户个人信息
			    this.userInfo = e.detail.userInfo,
			    this.hasUserInfo = true
			  },
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
