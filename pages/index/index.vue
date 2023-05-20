<template>
	<view class="page">
		<view style="display: flex;border-bottom: 1px solid #ccc;background-color: #FFF;">
			<input type="text" v-model="sku" placeholder="货号" placeholder-style="color: #FFF;"
				style="margin-left: 20rpx;background-color: #ccc;border-radius: 4px;height: 40rpx;line-height: 40rpx;margin-right: 10px;">
			<view @click="sousuo" style="color:orange;font-size: 13px;">搜索</view>
			<view
				style="margin-left: 20rpx;border:1px solid blue;background-color: orange;width:70rpx;text-align: center;border-radius: 7px;color: #FFF;font-size: 13px;height: 40rpx;line-height: 40rpx;"
				@click="tiaodian">店</view>
			<view
				style="height: 40rpx;line-height: 40rpx;margin-left: 20rpx;width:70rpx;border:1px solid blue;background-color: orange;text-align: center;border-radius: 7px;color: #FFF;font-size: 13px;"
				@click="shai">筛</view>
		</view>
		<view
			style="display: flex;border-bottom: 1px solid #ccc;background-color: #FFF;height: 70rpx;align-items: center;">
			<view style="display: flex;justify-content: center;align-items: center;" @click="jiage">
				<view style="margin: 10px;">价格</view>
				<view style="height: 60rpx;font-size: 10px;margin-left: -7px;">
					<view class="hong1" :class="{ 'active': jg == 1?true:false }" style="margin-bottom: -6px;">∧</view>
					<view class="hong1" :class="{ 'active': jg == 2?true:false }">∨</view>
				</view>
			</view>
			<view style="display: flex;justify-content: center;align-items: center;" @click="kucunshuliang">
				<view style="margin: 10px;">库存数量</view>
				<view style="height: 60rpx;font-size: 10px;margin-left: -7px;">
					<view class="hong" :class="{ 'active1': kcsl == 1?true:false }" style="margin-bottom: -6px;">∧
					</view>
					<view class="hong" :class="{ 'active1': kcsl == 2?true:false }">∨</view>
				</view>
			</view>

		</view>
		<view v-for="(item,idx) in pagedata" style="background-color: white;margin-top: 10px;">

			<view style="display: flex;border-bottom: 1px solid #ccc;margin-top: 30rpx;"
				@click="tiao(item.store,item.lianjie,item.sku,item.des,item.yuanjia,item.xianjia,item.dpshuliang,item.category)">
				<view>
					<image :src="item.lianjie" mode="aspectFit" style="width: 230rpx;height:200rpx"></image>
					<view
						style="font-size: 12px;color: #FFF;text-align: center;border-radius: 7px;background-color: indianred;"
						v-show="item.huodongjia">{{item.lineup}}</view>
				</view>
				<view>
					<view style="font-weight: bold;">货号：{{item.sku}}</view>
					<view style="font-weight: 500;font-size: 16px;height: 16px;display: flex;align-items: center;">
						<view>商品名称：</view>
						<view style="font-size: 9px;height: 16px;line-height: 16px;">{{item.des}}</view>
					</view>
					<view style="margin-top: 20rpx;font-size: 11px;color:red">店铺：{{item.store}}</view>

					<view style="display: flex;align-items: center;justify-content: flex-start;">
						<view style="font-size: 16px;color:red">现价：{{item.xianjia}}</view>
						<view style="font-size: 12px;margin-left: 30rpx;">原价：{{item.yuanjia}}</view>

					</view>

					<!-- 	<view style="font-size: 12px;"  v-show="item.huodongjia">活动价：{{item.huodongjia}}</view> -->

					<view style="font-size: 12px;" v-show="item.huodongjia">
						折扣价：{{(item.bili*item.huodongjia).toFixed(2)}}</view>
					<view style="display: flex;align-items: center;justify-content: space-around;">
						<view style="font-size: 13px;margin-right: 10px;">当前店铺库存：{{item.dpshuliang}}</view>
						<view style="font-size: 13px;">全国库存：{{item.quanguoshuliang}}</view>
					</view>
				</view>
			</view>

		</view>
		<uni-pagination @change="change" :current="current" :total="total" :page-size="pageSize" prev-text="前一页"
			next-text="后一页" />

		<view class="zzc_mol" v-if="isRecording"></view>
		<watermark color="#caccc9" :texts="key" opacity="0.4"></watermark>
	</view>
</template>

<script>
	import watermark from "../../components/kxj-watermark/kxj-watermark.vue"
	export default {
		components: {
			watermark
		},
		data() {
			return {
				isRecording: false,
				current: 1,
				total: 99999999,
				pageSize: 10,
				pagedata: "",
				sku: "",

				fuzhuang: true,
				peijian: true,
				xie: true,
				kucun: true,
				jg: 3,
				kcsl: 3,
				key: []

			}

		},
		created() {
			this.screenInit();
		},
		onUnload() {

			// this.AllowScreenshots()
		},

		async created() {

			this.key.push(uni.getStorageSync("key"))
			console.log(uni.getStorageSync("shuzu"))
			// if(uni.getStorageSync("shuzu").length!=0){
			// 	await this.dianpuqingkuang()
			// }else{
			// console.log("1111")
			// await this.loadDatamy()
			// }


		},
		async onLoad() {

			if (uni.getStorageSync("shuzu").length == 0) {
				await this.$http('dianpuall', {

				}, {
					method: "GET"
				}).then(res => {

					// console.log(res)
					// this.dianpulist=res
					let qq = []
					for (let i = 0; i < res.length; i++) {
						qq.push(res[i].store);
					}
					console.log(qq);
					uni.setStorageSync("shuzu", qq)
				}).catch(err => {
					console.error(err)
				})


			}

			await this.loadDatamy()
			// if(!uni.getStorageSync("key")){
			//  uni.navigateTo({
			//  	url:"/pages/come/come"
			//  })
			// }
			// uni.setStorageSync("shuzu",[])
			// uni.setStorageSync("kucun",this.kucun)
			// uni.setStorageSync("fuzhuang",this.fuzhuang)
			// uni.setStorageSync("xie",this.xie)
			// uni.setStorageSync("peijian",this.peijian)

		},
		methods: {
			kucunshuliang() {
				this.jg = 3
				if (this.kcsl === 1) {
					this.kcsl = 2
				} else if (this.kcsl === 2) {
					this.kcsl = 3
				} else {
					this.kcsl = 1
				}

				// 根据当前的排序类型更新列表展示
				this.loadDatamy()
			},
			jiage() {
				this.kcsl = 3
				if (this.jg === 1) {
					this.jg = 2
				} else if (this.jg === 2) {
					this.jg = 3
				} else {
					this.jg = 1
				}

				// 根据当前的排序类型更新列表展示
				this.loadDatamy()
			},
			jietu() {
				this.$http('guoqi', {
					key: uni.getStorageSync("key")
				}, {
					method: "GET"
				}).then(res => {
					console.log(res)
					uni.reLaunch({
						url: "/pages/come/come"
					})

				}).catch(err => {
					console.error(err)
				})
			},
			async dianpuqingkuang() {
				await this.$http('dianpu', {
					page: 1,
					shuzu: JSON.stringify(uni.getStorageSync("shuzu"))
				}, {
					method: "POST"
				}).then(res => {
					console.log(typeof(res.total))
					if (typeof(res.total) === 'number') {
						this.total = res.total
					} else {
						this.total = Math.max(...res.total)
					}

					this.pagedata = res.data

					for (let i = 0; i < this.pagedata.length; i++) {
						this.load2(i)

						this.load3(i)
					}

				}).catch(err => {
					console.error(err)
				})
			},
			loadDatamy() {
				this.$http('quanbu', {
					page: 1,
					sku: this.sku,
					fuzhuang: !uni.getStorageSync("fuzhuang"),
					xie: !uni.getStorageSync("xie"),
					peijian: !uni.getStorageSync("peijian"),
					youhui: !uni.getStorageSync("kucun"),
					shuzu: JSON.stringify(uni.getStorageSync("shuzu")),
					kcls: this.kcsl,
					jg: this.jg
				}, {
					method: "GET"
				}).then(async res => {
					console.log(res)
					// console.log(typeof(res.total))
					// if(typeof(res.total)==='number'){
					// 	this.total=res.total
					// }else{
					// 	this.total = Math.max(...res.total)
					// }

					this.pagedata = res.data

					this.qq();

				}).catch(err => {
					console.error(err)
				})
			},

			qq() {
				for (let i = 0; i < this.pagedata.length; i++) {
					this.load2(i)

					this.load3(i)
				}
			},
			shai() {
				// uni.removeStorageSync('shuzu');
				uni.navigateTo({
					url: "/pages/shai/shai"
				})
			},
			tiaodian() {

				// uni.removeStorageSync('shuzu');
				uni.navigateTo({
					url: "/pages/dian/dian"
				})
			},
			async sousuo() {
				uni.removeStorageSync('shuzu');
				this.current = 1
				await this.$http('quanbu', {
					page: 1,
					sku: this.sku
				}, {
					method: "GET"
				}).then(res => {
					console.log(res)
					console.log("111111111")
					this.pagedata = res.data

					this.qq();
				}).catch(err => {
					console.error(err)
				})
			},
			tiao(store, lianjie, sku, des, yuanjia, xianjia, dpshuliang, category) {
				console.log(lianjie)
				uni.setStorageSync("lianjie", lianjie)
				uni.navigateTo({
					url: "../detail/detail?store=" + store + "&sku=" + sku + "&des=" + des + "&yuanjia=" +
						yuanjia + "&xianjia=" + xianjia + "&dpshuliang=" + dpshuliang + "&category=" + category
				})


			},

			async loadData1() {
				await this.$http('quanbu', {
					page: 1,
					sku: this.sku,
					fuzhuang: uni.getStorageSync("fuzhuang"),
					xie: uni.getStorageSync("xie"),
					peijian: uni.getStorageSync("peijian"),
					youhui: uni.getStorageSync("kucun")
				}, {
					method: "GET"
				}).then(res => {
					console.log(typeof(res.total))
					if (typeof(res.total) === 'number') {
						this.total = res.total
					} else {
						this.total = Math.max(...res.total)
					}

					this.pagedata = res.data

					for (let i = 0; i < this.pagedata.length; i++) {
						this.load2(i)

						this.load3(i)
					}

				}).catch(err => {
					console.error(err)
				})
			},
			async load2(i) {
				await this.$http('quanguokucun', {
					'sku': this.pagedata[i].sku,
					'category': this.pagedata[i].category
				}).then(res => {
					console.log(res)
					this.pagedata[i].quanguoshuliang = res
					this.$forceUpdate()
				}).catch(err => {
					console.error(err)
				})

			},
			async load3(i) {
				await this.$http('youhui', {
					'sku': this.pagedata[i].sku
				}).then(res => {
					console.log(res)
					this.pagedata[i].huodongjia = res[0].huodongjia
					this.pagedata[i].lineup = res[0].lineup
					this.pagedata[i].bili = res[0].bili
					this.$forceUpdate()
				}).catch(err => {
					console.error(err)
				})
			},
			async change(e) {
				console.log(e)
				uni.pageScrollTo({
					scrollTop: 0,
					duration: 300
				});
				this.current = e.current

				let data = {
					page: this.current,
					sku: this.sku,
				}
				if (!this.sku.length > 0) {
					data = {
						page: this.current,
						sku: this.sku,
						fuzhuang: !uni.getStorageSync("fuzhuang"),
						xie: !uni.getStorageSync("xie"),
						peijian: !uni.getStorageSync("peijian"),
						youhui: !uni.getStorageSync("kucun"),
						shuzu: JSON.stringify(uni.getStorageSync("shuzu")),
						kcls: this.kcsl,
						jg: this.jg
					}
				}

				this.$http('quanbu',data , {
					method: "GET"
				}).then(async res => {
					console.log(res)
					// console.log(typeof(res.total))
					// if(typeof(res.total)==='number'){
					// 	this.total=res.total
					// }else{
					// 	this.total = Math.max(...res.total)
					// }

					this.pagedata = res.data

					this.qq();

				}).catch(err => {
					console.error(err)
				})




				// if(uni.getStorageSync("shuzu").length==0){


				// 			await this.$http('quanbu', {
				// 				page: this.current,
				// 				sku:this.sku,
				// 			fuzhuang:!uni.getStorageSync("fuzhuang"),
				// 			xie:!uni.getStorageSync("xie"),
				// 			peijian:!uni.getStorageSync("peijian"),
				// 			youhui:!uni.getStorageSync("kucun"),
				// 					shuzu:JSON.stringify(uni.getStorageSync("shuzu")),
				// 					kcls:this.kcsl,
				// 					jg:this.jg
				// 			}, {
				// 				method: "GET"
				// 			}).then(res => {
				// 				console.log(res)
				// if(typeof(res.total)==='number'){
				// 	this.total=res.total
				// }else{
				// 	this.total = Math.max(...res.total)
				// }
				// 				this.pagedata = res.data

				// 				for (let i = 0; i < this.pagedata.length; i++) {

				// 					this.load2(i)
				// 					this.load3(i)
				// 				}
				// 			}).catch(err => {
				// 				console.error(err)
				// 			})
				// 			}else{
				// 				await this.$http('dianpu', {
				// 						page: this.current,
				// 							shuzu:JSON.stringify(uni.getStorageSync("shuzu"))
				// 					}, {
				// 						method: "POST",

				// 					}).then(res => {
				// 		console.log(res)
				// if(typeof(res.total)==='number'){
				// 	this.total=res.total
				// }else{
				// 	this.total = Math.max(...res.total)
				// }
				// 			this.pagedata = res.data

				// 			for (let i = 0; i < this.pagedata.length; i++) {

				// 				this.load2(i)
				// 				this.load3(i)
				// 			}
				// 		}).catch(err => {
				// 			console.error(err)
				// 		})
				// }
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

									// console.log(res)
									// console.log(new Date())
								},
								fail: (err) => {
									console.log(err)

									// _this.jietu()
									// console.log(new Date())
								},
								complete: (res) => {
									console.log(res)
									// _this.jietu()
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

	.example-body {
		/* #ifndef APP-NVUE */
		display: block;
		/* #endif */
	}

	.btn-view {
		/* #ifndef APP-NVUE */
		display: flex;
		flex-direction: column;
		/* #endif */
		padding: 15px;
		text-align: center;
		background-color: #fff;
		justify-content: center;
		align-items: center;
	}

	.btn-flex {
		display: flex;
		flex-direction: row;
		justify-content: center;
		align-items: center;
	}

	.button {
		margin: 20px;
		width: 150px;
		font-size: 14px;
		color: #333;
	}

	.hong {
		color: #ccc
	}

	.active {
		color: red !important;
	}

	.hong1 {
		color: #ccc
	}

	.active1 {
		color: red !important;
	}
</style>
