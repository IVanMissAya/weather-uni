<template>
	<view>
		<view class="waveWrapper waveAnimation">
			<view class="waveWrapperInner bgTop"><view class="wave waveTop" style="background-image: url('http://front-end-noobs.com/jecko/img/wave-top.png')"></view></view>
			<view class="waveWrapperInner bgMiddle"><view class="wave waveMiddle" style="background-image: url('http://front-end-noobs.com/jecko/img/wave-mid.png')"></view></view>
			<view class="waveWrapperInner bgBottom"><view class="wave waveBottom" style="background-image: url('http://front-end-noobs.com/jecko/img/wave-bot.png')"></view></view>
		</view>
	</view>
</template>

<script>
export default {
	name: 'bottomWave',
	data() {
		return {};
	}
};
</script>

<style lang="scss" scoped>
@keyframes move_wave {
	0% {
		transform: translateX(0) translateZ(0) scaleY(1);
	}
	50% {
		transform: translateX(-25%) translateZ(0) scaleY(0.55);
	}
	100% {
		transform: translateX(-50%) translateZ(0) scaleY(1);
	}
}
.waveWrapper {
	overflow: hidden;
	position: absolute;
	left: 0;
	right: 0;
	bottom: 0;
	top: 0;
	margin: auto;
}
.waveWrapperInner {
	position: absolute;
	width: 100%;
	overflow: hidden;
	height: 100%;
	bottom: 0px;
	// background-image: linear-gradient(to top, #86377b 20%, #27273c 80%);
}
.bgTop {
	z-index: 15;
	opacity: 0.5;
}
.bgMiddle {
	z-index: 10;
	opacity: 0.75;
}
.bgBottom {
	z-index: 5;
}
.wave {
	position: absolute;
	left: 0;
	width: 200%;
	height: 100%;
	background-repeat: repeat no-repeat;
	background-position: 0 bottom;
	transform-origin: center bottom;
}
.waveTop {
	background-size: 50% 80rpx;
}
.waveAnimation .waveTop {
	animation: move-wave 3s;
	-webkit-animation: move-wave 3s;
	-webkit-animation-delay: 1s;
	animation-delay: 1s;
}
.waveMiddle {
	background-size: 50% 100rpx;
}
.waveAnimation .waveMiddle {
	animation: move_wave 10s linear infinite;
}
.waveBottom {
	background-size: 50% 80rpx;
}
.waveAnimation .waveBottom {
	animation: move_wave 15s linear infinite;
}
</style>
