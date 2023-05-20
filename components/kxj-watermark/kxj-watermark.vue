<template>
	<view class="watermarks"  :style="{'opacity': opacity,'height':height,'position': position, 'bottom': bottom, 'z-index': zindex }">
		<view class="watermarksshell">
			<block v-for="(t, i) in count" :key="'watermark' + i">
				<view :style="{ transform: 'rotate(' + rotate + 'deg)' }">
					<image
						v-if="logo != '' || logo != 'none'"
						:src="logo"
						:style="{ width: size * texts.length + 'em', height: size * texts.length + 'em', float: 'left' }"
					></image>
					<view :style="{ 'padding-left': logo != '' || logo != 'none' ? size * texts.length + 0.2 + 'em' : 0 + 'em' }">
						<view v-for="(text, c) in texts" :key="'wmtext' + c" :style="{ color: color, 'font-size': size + 'em', 'line-height': size + 'em' }">{{ text }}</view>
					</view>
				</view>
			</block>
		</view>
	</view>
</template>

<script>
export default {
	name: 'kxj-watermark',
	props: {
		//水印类型:fixation固定，follow随动
		type: {
			type: String,
			default: 'fixation'
		},

		//高度，只有当type=follow时才需要设置
		height: {
			type: String,
			default: '100%'
		},

		//水印内容
		texts: {
			type: Array,
			//required:true, //必填参数
			default: () => ['']
		},

		//图标，自带三种颜色的图标
		logo: {
			type: String,
			default: ''
		},

		//设置水印数量（密集度）,最好设置为偶数,数字越大水印越密集
		count: {
			type: Number,
			default: 12
		},

		//透明度,0到1之间。
		opacity: {
			type: Number,
			default: 0.5
		},

		//文字大小,单位是em
		size: {
			type: Number,
			default: 1
		},

		//文字颜色
		color: {
			type: String,
			default: '#d9d9d9'
		},

		//旋转角度
		rotate: {
			type: Number,
			default: -35
		},

		//层级
		zindex: {
			type: String,
			default: '996'
		}
	},

	data() {
		return {
			position: 'fixed',
			width: '150%',
			bottom: 0
		};
	},
	beforeMount() {
		if (this.type === 'follow') {
			this.position = 'absolute';
			(this.width = '150%'), (this.bottom = 'auto');
		}
	}
};
</script>

<style lang="css">
.watermarks {
	top: 0;
	left: 0;
	right: 0;
	width: 100%;
	height: 100%;
	overflow: hidden;
	pointer-events: none;
	
}
.watermarksshell {
	display: flex;
	flex-wrap: wrap;
	pointer-events: none;
	top: 0;
	left: 0;
	right: 0;
	width: 150%;
	height: 130%;
	height: inherit;
}
</style>
