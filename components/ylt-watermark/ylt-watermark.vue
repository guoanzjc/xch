<template>
	<view class="content">
		<view class="water_top">
			<canvas canvas-id="watermark" style="width: 100%;height: 100%;"></canvas>
		</view>
		<view style="width: 100%;height: 100%;position: fixed;z-index: 10;">
			<slot></slot>
		</view>
	</view>
</template>

<script>
export default {
	data: () => ({}),
	props: {
		title: {
			type: String,
			default: () => ''
		},
		mobile: {
			type: String,
			default: () => ''
		},
		appName: {
			type: String,
			default: () => ''
		}
	},
	mounted() {
		this.$nextTick(() => {
			this.createWatemark();
		})
	},
	methods: {
		createWatemark() {
			let ctx = uni.createCanvasContext('watermark', this);
			const title = this.title;
			const mobile = this.mobile;
			const appName = this.appName;
			console.log(title,appName,mobile)
			ctx.rotate((45 * Math.PI) / 185);
			ctx.setFontSize(24);
			ctx.setFillStyle('#000');
			for (let j = 1; j < 8; j++) {
				//用for循环达到重复输出文字的效果，这个for循环代表纵向循环
				ctx.beginPath();
				ctx.setFontSize(10);
				ctx.setFillStyle('rgba(169,169,169,.2)');

				ctx.fillText(title, 0, 100 * j);
				ctx.setFontSize(10);
				ctx.fillText(mobile, 0, 100 * j + 20);
				ctx.fillText(appName, 0, 100 * j + 40);
				for (let i = 1; i < 8; i++) {
					//这个for循环代表横向循环，
					ctx.beginPath();
					ctx.setFontSize(16);
					ctx.setFillStyle('rgba(169,169,169,.6)');
					ctx.fillText(title, 85 * i, 100 * j);
					ctx.setFontSize(10);
					ctx.fillText(mobile, 85 * i + 20, 100 * j + 20);
					ctx.fillText(appName, 85 * i + 40, 100 * j + 40);
				}
			}
			for (let j = 0; j < 8; j++) {
				ctx.beginPath();
				ctx.setFontSize(16);
				ctx.setFillStyle('rgba(169,169,169,.6)');

				ctx.fillText(title, 0, -100 * j);
				ctx.setFontSize(10);
				ctx.fillText(mobile, 0, -100 * j + 20);
				ctx.fillText(appName, 0, -100 * j + 20);
				for (let i = 1; i < 8; i++) {
					ctx.beginPath();
					ctx.setFontSize(16);
					ctx.setFillStyle('rgba(169,169,169,.6)');
					ctx.fillText(title, 85 * i, -100 * j);
					ctx.setFontSize(10);
					ctx.fillText(mobile, 85 * i + 20, -100 * j + 20);
					ctx.fillText(appName, 85 * i + 40, -100 * j + 40);
				}
			}

			ctx.draw();
			console.log(ctx);
		}
	}
};
</script>

<style>
.water_top {
	position: fixed;
	z-index: 1;
	top: 0;
	right: 0;
	bottom: 0;
	left: 0;
	/* background-color: #007aff; */
}
</style>
