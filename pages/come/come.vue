<template>
	<view class="page">
		<view style="width: 750rpx;height: 100vh;text-align: center;">
			<input v-model="key" placeholder="请输入您的cdkey" style="margin-top: 300rpx;" />
			<button @click="yanzheng" style="margin-top: 30rpx;">
				确定
			</button>
		</view>

		<view class="zzc_mol" v-if="isRecording"></view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				isRecording: false,
				key: ""
			}
		},
		created() {
			this.screenInit();
		},
		
		onLoad() {
			this.key = uni.getStorageSync("key")
			if (this.key) {
				this.yanzheng()
			}

		},
		methods: {
			yanzheng() {
				uni.login({
					provider: 'weixin', //使用微信登录
					success: (res) => {
						let code = res.code
						// 通过code换取openId
						this.$http('huanopenid', {
							code: code
						}, {
							method: "GET"
						}).then(res => {
							console.log(res)
							let openid = res
							
							this.$http('check1', {
								key: this.key,
								openid: openid
							}, {
								method: "GET"
							}).then(res => {
								console.log(res)
								if (res == "已绑定其他微信号") {
									uni.showToast({
										icon: "none",
										title: res
									})

								} else {
									if (res.code == 0) {
										uni.setStorageSync("key", this.key)
										uni.setStorageSync("time", res.data.end_at)
										uni.switchTab({
											url: "/pages/user/user"
										})

									} else {
										uni.showToast({
											icon: "none",
											title: "无效的key"
										})
									}
								}

								// this.kaizhe=res[0].kaizhe

							}).catch(err => {

								console.error(err)
							})
})}})


							// this.kaizhe=res[0].kaizhe

					
					

				

				// 	wx.request({
				// 			url: 'https://api.weixin.qq.com/sns/jscode2session?appid=wxcbf33d0e03b886f6&secret=c22c620bd782b9a4095583d57fad83b5&js_code=' +
				// 				code + '&grant_type=authorization_code',
				// 			success: (res) => {
				// 				console.log(res);
				// 				let openid = res.data.openid
				// 				console.log(openid);


				// 			})
			


			},
			screenInit() {
				let _this = this;
				// #ifdef H5
				uni.showToast({
					title: '请在APP或者小程序环境下操作',
					icon: 'error'
				})
				// #endif
				// #ifndef H5
				uni.getSystemInfo({
					success: function(e) {
						// #ifdef APP-PLUS
						if (e.platform == 'android') {
							//注意：一旦开启禁止截屏/录屏后将会全局生效，关闭页面时记得放开允许截屏/录屏
							_this.NoscreenCapture();
						} else {
							//在APP IOS端截屏、录屏暂时没办法实现，可以寻求原生解决
						}
						// #endif

						// #ifdef MP-WEIXIN
						if (e.platform == 'android') {


							//微信小程序在安卓手机上 禁止截屏/录屏(注意：禁止后录屏动作还是会进行，但录屏后的视频是黑屏的)
							wx.setVisualEffectOnCapture({
								visualEffect: 'hidden', //传入 hidden 则表示在截屏/录屏时隐藏屏幕
								success: (res) => {
									console.log(res)
									console.log(new Date())
								},
								fail: (err) => {
									console.log(err)
									// console.log(new Date())
								},
								complete: (res) => {
									console.log(res)
									// console.log(new Date())
								}
							})
						} else {
							//微信小程序在IOS手机上，注意：目前微信小程序在ios上也只能通过监听录屏状态，然后通过添加view层来进行阻止，截屏暂时无法实现
							//监听用户录屏事件
							wx.onScreenRecordingStateChanged(function(res) {
								console.log(res)
								if (res.state == 'start') {
									_this.isRecording = true
								} else {
									_this.isRecording = false
								}
							})
							wx.onUserCaptureScreen(function(res) {
								console.log(res)
								_this.isRecording = true
								console.log(new Date())

							})

							//查询用户是否在录屏
							wx.getScreenRecordingState({
								success: (res) => {
									if (res.state == 'on') {
										_this.isRecording = true
									} else if (res.state == 'off') {
										_this.isRecording = false
									}
								},
								fail: (err) => {
									_this.isRecording = false
								}
							})
						}
						// #endif
					}
				})
				// #endif
			},
				}
	}
</script>

<style>
	.logo {
		width: 100%;
	}

	.zzc_mol {
		background: #000;
		width: 100%;
		height: 100%;
		position: fixed;
		z-index: 99999;
		top: 0;
		left: 0;
	}
</style>
