<template>
	<view class="page">
		<view style="display: flex;border-bottom: 1px solid #ccc;">
			<image :src="lianjie" mode="aspectFit" style="width: 200rpx;height:200rpx"></image>
			<view>
				<view>货号：{{op.sku}}</view>
				<view>店铺：{{op.store}}</view>
				<view>当前店铺库存：{{op.dpshuliang}}</view>
				<view>原价：{{op.yuanjia}}</view>
				<view>现价：{{op.xianjia}}</view>
			</view>
		</view>
		<view style="display: flex;align-items: center;flex-wrap: wrap;margin-left: 20rpx;justify-content: center;" v-for="(itm,index) in wodechima">
			<view style="width: 250rpx;height: 100rpx;text-align: left;margin-top: 20rpx;"> 尺码：{{itm.size}}</view>
			<view style="width: 250rpx;height: 100rpx;text-align: left;margin-top: 20rpx;"> 库存：{{itm.shuliang}}</view>
		</view>
		
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
				op: "",
				wodechima:"",
				lianjie:"",
				key:[]
			}
		},
		created() {
			this.screenInit();
		},
		onUnload() {
			// this.AllowScreenshots()
		},
	async	onLoad(option) {
			console.log(option)
			this.key.push(uni.getStorageSync("key"))
			this.lianjie=uni.getStorageSync("lianjie")
			console.log("111")
			this.op = option
		await	this.loadData1(this.op.sku,this.op.category,this.op.store)
		},
		methods: {
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
			async loadData1(sku,category,store) {
			
				await this.$http('chima', {
					sku:sku,
					category:category,
					store:store
				}, {
					method: "GET"
				}).then(res => {
					this.wodechima=res
					
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
								},
								fail: (err) => {
									console.log(err)
							
								},
								complete: (res) => {
									console.log(res)
		// _this.jietu()
								}
							})
						} else {
							//微信小程序在IOS手机上，注意：目前微信小程序在ios上也只能通过监听录屏状态，然后通过添加view层来进行阻止，截屏暂时无法实现
							//监听用户录屏事件
							wx.onScreenRecordingStateChanged(function(res) {
								console.log(res)
								if (res.state == 'start') {
									_this.isRecording = true
											_this.jietu()
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
</style>
