<template>
	<view class="page" style="margin-left: 20rpx;">
		<view style="margin-top: 20rpx;font-weight: bold">类型</view>
		<view style="display: flex;">
			<view :class="{'active':kucun}" style="border: 1px solid #ccc;color:white;background-color: #ccc;" @click="kucun1">库存</view>
			<view :class="{'active':!kucun}" style="margin-left: 20rpx;border: 1px solid #ccc;color:white;background-color: #ccc;" @click="kucun2">{{kaizhe}}</view>
		</view>
		<view style="margin-top: 20rpx;font-weight: bold">类别</view>
		<view style="display: flex;">
			<view :class="{'active':!fuzhuang}" style="border: 1px solid #ccc;color:white;background-color: #ccc;" @click="fuzhuang1">服装</view>
			<view  :class="{'active':!xie}" style="margin-left: 20rpx;border: 1px solid #ccc;color:white;background-color: #ccc;" @click="xie1">鞋</view>
			<view  :class="{'active':!peijian}" style="margin-left: 20rpx;border: 1px solid #ccc;color:white;background-color: #ccc;" @click="peijian1">配件</view>
		</view>
		<button style="margin-top: 20rpx;background-color: orange;color:white" @click="queding">确定</button>
		<view class="zzc_mol" v-if="isRecording"></view>
		<watermark color="#caccc9" :texts="key" opacity="0.4" ></watermark>
	</view>
</template>

<script>
	import watermark  from "../../components/kxj-watermark/kxj-watermark.vue"
	export default {
		components: {
		        watermark
		    },
		data() {
			return {
isRecording: false,
kaizhe:"",
kucun:true,
fuzhuang:true,
xie:true,
peijian:true,
key:[]

			}
		},
		created() {
					this.screenInit();
				},
				onUnload() {
					// this.AllowScreenshots()
				},
		onLoad() {
			this.key.push(uni.getStorageSync("key"))
this.kaizhetime()
		},
		methods: {
			queding(){
				uni.setStorageSync("kucun",this.kucun)
				uni.setStorageSync("fuzhuang",this.fuzhuang)
				uni.setStorageSync("xie",this.xie)
				uni.setStorageSync("peijian",this.peijian)
				uni.reLaunch({
				url:"/pages/index/index"
				})
			},
			kucun1(){
				this.kucun=!this.kucun
			},
			kucun2(){
				this.kucun=!this.kucun
			},
			fuzhuang1(){
				this.fuzhuang=!this.fuzhuang
			},
			xie1(){
				this.xie=!this.xie
			},
			peijian1(){
				this.peijian=!this.peijian
			},
			kaizhetime(){
			 this.$http('kaizhe', {
					
				}, {
					method: "GET"
				}).then(res => {
					console.log(res)
					this.kaizhe=res[0].kaizhe
					
				}).catch(err => {
					console.error(err)
				})
			},
			jietu(){
				this.$http('guoqi', {
									key:uni.getStorageSync("key")
								}, {
									method: "GET"
								}).then(res => {
									console.log(res)
									uni.reLaunch({
										url:"/pages/come/come"
									})
									
								}).catch(err => {
									console.error(err)
								})
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
									// _this.jietu()
									console.log(new Date())
								},
								fail: (err) => {
									console.log(err)
									
									// console.log(new Date())
								},
								complete: (res) => {
									// _this.jietu()
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
									_this.jietu()
									_this.isRecording = true
								} else {
									_this.isRecording = false
								}
							})
							wx.onUserCaptureScreen(function(res) {
							   console.log(res)
								   _this.isRecording = true
							  console.log(new Date()) 
								_this.jietu()
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
			//安卓端禁止截屏
			NoscreenCapture() {
				let osname = plus.os.name;
				if (osname == "Android") {
					var activity = plus.android.runtimeMainActivity();
					plus.android.invoke(plus.android.invoke(activity, "getWindow"), "addFlags", 0x00002000);
				}
			},
			//安卓端允许截屏  
			AllowScreenshots() {
				let osname = plus.os.name;
				if (osname == "Android") {
					var activity = plus.android.runtimeMainActivity();
					plus.android.invoke(plus.android.invoke(activity, "getWindow"), "clearFlags", 0x00002000);
				}
			}
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
	.active{
		color:white!important;
		background-color: orange!important;
	}
</style>
