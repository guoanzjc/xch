<template>
	<view class="page">
		<view style="display: flex;width: 700rpx;height: auto;justify-content: center;align-items: center;flex-wrap: wrap;" >
		<text style="margin: 10px;">已选店铺：</text>
			<view v-for="(itm,index) in yixuanlist" @click="shanchu(itm)" style="width: 150rpx;height:100rpx;border: 1px solid #ccc;text-align: center;line-height: 100rpx;margin: 10px;font-size: 10px;">
				{{itm}}
			</view>
		</view>
		<button @click="cha" style="background-color: orange;color: white;">查询</button>
		<view style="border: 2px solid #000;width: 750rpx;"></view>
		<view style="display: flex;width: 700rpx;height: auto;justify-content: center;align-items: center;flex-wrap: wrap;" >
			<view v-for="(itm,index) in dianpulist" @click="shang(itm.store)" style="width: 150rpx;height:100rpx;border: 1px solid #ccc;text-align: center;line-height: 100rpx;margin: 10px;font-size: 10px;" >
				{{itm.store}}
			</view>
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
dianpulist:[],
yixuanlist:[],
list:[],
key:[]
			}
		},
		created() {
					this.screenInit();
				},
				onUnload() {
					// this.AllowScreenshots()
				},
	async	onLoad() {
		this.key.push(uni.getStorageSync("key"))
		console.log("2222")
		// this.yixuanlist=uni.getStorageSync("shuzu")
		
			
			// console.log(typeof(this.yixuanlist))
await this.dian()
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
			cha(){
				uni.setStorageSync("shuzu",this.yixuanlist)
				uni.reLaunch({
				url:"/pages/index/index"
				})
				
			},
			shanchu(word){
 let newArr = this.yixuanlist.filter((item, i, arr) => {
  //函数本身返回布尔值，只有当返回值为true时，当前项存入新数组。
	return item != word
  })
  this.yixuanlist=newArr
  uni.showToast({
  	icon:'none',
  	title:"已删除"+word
  })
			},
		shang(word){
			if(this.yixuanlist.includes(word)){
				uni.showToast({
					icon:'none',
					title:"勿重复选择"
				})
			}else{
				this.yixuanlist.push(word)
				uni.showToast({
					icon:'none',
					title:"已增加"+word
				})
			}
			
		},
			async	dian(){
				
				await	this.$http('dianpuall', {
						
					}, {
						method: "GET"
					}).then(res => {
	
						
						this.dianpulist=res
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
</style>
