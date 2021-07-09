<template>
	<view class="content">
		<!-- 选择地区 -->
		<view class="u-flex u-row-center u-padding-top-20" @click="showPicker = true">
			<u-icon name="map" color="#fff" size="40"></u-icon>
			<view class="u-margin-left-10  u-font-30 u-main-color">{{ locationName }}</view>
		</view>
		<!-- 天气ICON -->
		<view class="u-flex u-row-between" style="margin:0 10%">
			<view class="u-flex u-margin-top-50  weather_icon">
				<view class="u-margin-right-80">
					<!-- 晴天 -->
					<view v-if="cur_img_value === 0">
						<view class="icon sunny" :data-desc="weather.cur_desc" @click="showDesc">
							<view class="sun"><view class="rays"></view></view>
						</view>
					</view>
					<!-- 多云 -->
					<view v-else-if="cur_img_value === 1">
						<view class="icon sun-shower" :data-desc="weather.cur_desc" @click="showDesc">
							<view class="cloud"></view>
							<view class="sun"><view class="rays"></view></view>
						</view>
					</view>
					<!-- 阴天 -->
					<view v-else-if="cur_img_value === 2">
						<view class="icon cloudy" :data-desc="weather.cur_desc" @click="showDesc">
							<view class="cloud"></view>
							<view class="cloud"></view>
						</view>
					</view>
					<!-- 阵雨 -->
					<view v-else-if="cur_img_value === 3">
						<view class="icon sun-shower" :data-desc="weather.cur_desc" @click="showDesc">
							<view class="cloud"></view>
							<view class="sun"><view class="rays"></view></view>
							<view class="rain"></view>
						</view>
					</view>
					<!-- 雷阵雨 -->
					<view v-else-if="cur_img_value === 4 || cur_img_value === 5">
						<view class="icon thunder-storm" :data-desc="weather.cur_desc" @click="showDesc">
							<view class="cloud"></view>
							<view class="lightning">
								<view class="bolt"></view>
								<view class="bolt"></view>
							</view>
						</view>
					</view>
					<!-- 雨 -->
					<view v-else-if="cur_img_value > 6 && cur_img_value < 13">
						<view class="icon rainy" :data-desc="weather.cur_desc" @click="showDesc">
							<view class="cloud"></view>
							<view class="rain"></view>
						</view>
					</view>
					<view v-else-if="cur_img_value > 20 && cur_img_value < 26">
						<view class="icon rainy" :data-desc="weather.cur_desc" @click="showDesc">
							<view class="cloud"></view>
							<view class="rain"></view>
						</view>
					</view>
					<!-- 雨 -->
					<view v-else-if="cur_img_value === 301">
						<view class="icon rainy" :data-desc="weather.cur_desc" @click="showDesc">
							<view class="cloud"></view>
							<view class="rain"></view>
						</view>
					</view>
					<!-- 其他情况 -->
					<view v-else>
						<view class="icon other_wea" :data-desc="weather.cur_wind_desc" @click="showDesc">
							<image :src="other_wea_icon" class="other_wea_icon" mode="widthFix"></image>
						</view>
					</view>
				</view>
				<!-- 实时温度 -->
				<view class="u-flex u-margin-left-20 cur_temp" data-desc="实时温度" @click="showDesc">
					<view class="cur_temp_value">{{ weather.cur_temp }}</view>
					<view class="cur_temp_unit">℃</view>
				</view>
			</view>
		</view>

		<!-- 天气详情 -->
		<view class="u-flex temp_info_area">
			<!-- 最高温、最低温 -->
			<view class="u-flex max_min_temp" data-desc="最高温/最低温" @click="showDesc">
				<view class="max_min_temp_icon"><image src="../../static/images/icons/temperature.png" class="top_icon" mode="widthFix"></image></view>
				<view class="u-flex maxmin_ali" style="align-items: center;">
					<view class="u-main-color u-margin-right-20">{{ weather.max_temp }}℃</view>
					<view class="u-main-color">/</view>
					<view class="u-main-color u-margin-left-20">{{ weather.min_temp }}℃</view>
				</view>
			</view>
			<!-- 湿度 -->
			<view class="u-flex max_min_temp" data-desc="空气湿度" @click="showDesc">
				<view class="max_min_temp_icon"><image src="../../static/images/icons/water.png" class="top_icon" mode="widthFix"></image></view>
				<view class="u-main-color u-margin-left-20">{{ weather.water_value }}%</view>
			</view>
			<!-- 风速 -->
			<view class="u-flex max_min_temp" :data-desc="weather.cur_wind_desc" @click="showDesc">
				<view class="max_min_temp_icon">
					<image src="../../static/images/icons/wind.png" style="width: 42rpx;height:42rpx" class="top_icon roating" mode="widthFix"></image>
				</view>
				<view class="u-flex u-main-color u-margin-left-20">
					<view>{{ weather.wind_unit }}</view>
					<view class="u-main-color ">{{ weather.wind_value }}</view>
				</view>
			</view>
		</view>

		<!-- 天气预报 -->
		<view class="u-flex feather-weather">
			<view class="u-flex top_fur_wea">
				<view class="u-flex-column sin_fur_wea" :key="index" v-for="(items, index) in weather.fur_day_wea">
					<view class="day_text">{{ items.day_text_value }}</view>
					<view class="day_wea_ico" :data-desc="items.day_wea_text" @click="showDesc"><image :src="items.day_wea_icon" class="daywea_icon" mode="widthFix"></image></view>
				</view>
			</view>
		</view>

		<!-- 图表区域 -->
		<view class="chart-area">
			<!-- #ifdef APP-PLUS ||H5 -->
			<echarts :option="option" style="width:100%;height: 100%;"></echarts>
			<!-- #endif -->

			<!-- #ifdef MP-WEIXIN -->
			<!-- <ec-canvas id="detailEc" type="2d" canvas-id="detailEc" :ec="ec"></ec-canvas> -->
			<uni-ec-canvas class="uni-ec-canvas" id="line-chart" ref="canvas" canvas-id="lazy-load-chart" :ec="ec"></uni-ec-canvas>
			<!-- #endif -->
		</view>
		
		<view class="bottom-area">
			<BottomWave></BottomWave>
		</view>
		
		<!-- 地区选择器 -->
		<u-picker v-model="showPicker" :default-region="defautlRegion" mode="region" @confirm="chooseCity"></u-picker>
		<Loading v-if="showLoading"></Loading>
	</view>
</template>

<script>
import Loading from '@/components/loading.vue';
import BottomWave from '@/components/bottomWave.vue';

// #ifdef APP-PLUS || H5
import echarts from '@/components/echarts/echarts.vue';
// #endif

// #ifdef MP-WEIXIN
let chartObject = null;
import uniEcCanvas from '@/components/uni-ec-canvas/uni-ec-canvas';
// #endif

export default {
	components: {
		Loading,
		BottomWave,
		// #ifdef APP-PLUS || H5
		echarts,
		// #endif
		// #ifdef MP-WEIXIN
		uniEcCanvas
		// #endif
	},
	data() {
		return {
			// #ifdef MP-WEIXIN
			ec: {
				lazyLoad: true,
				option: {
					tooltip: {
						trigger: 'axis',
						formatter: function(params, ticket, callback) {
							var tipString =
								params[0].axisValue + '\n' + params[0].seriesName + ':' + params[0].value + '℃' + '\n' + params[1].seriesName + ':' + params[1].value + '℃';
							return tipString;
						},
						/*设置x轴左右固定，上下跟随。*/
						position: function(point, params, dom, rect, size) {
							return [60, point[1]];
						}
					},
					grid: {
						left: '6%',
						right: '6%',
						top: '15%',
						bottom: '6%',
						containLabel: true
					},
					xAxis: {
						type: 'category',
						boundaryGap: false,
						data: ['2-12', '2-14', '2-16', '2-18', '2-20', '2-22', '2-24'],
						axisLine: {
							// y轴
							show: false
						},
						axisTick: {
							// y轴刻度线
							show: false
						},
						splitLine: {
							// 网格线
							show: false
						}					
					},
					yAxis: {
						type: 'value',
						axisLine: {
							// y轴
							show: false
						},
						axisTick: {
							// y轴刻度线
							show: false
						},
						splitLine: {
							// 网格线
							show: false
						},
						axisLabel: { formatter: '{value} ℃' }
					},
					color: ['#92DCC7', '#B2D494'],
					legend: {
						data: ['最高气温', '最低气温']
					},
					series: [
						{
							name: '最高气温',
							type: 'line',
							smooth: true,
							symbol: 'none',
							lineStyle: {
								color: '#92DCC7'
							},
							data: [28, 29, 31, 34, 30, 30, 29]
						},
						{
							name: '最低气温',
							type: 'line',
							smooth: true,
							symbol: 'none',
							lineStyle: {
								color: '#B2D494'
							},
							data: [24, 23, 20, 23, 20, 26, 23]
						}
					]
				}
			},
			// #endif
			showLoading: false,
			showPicker: false,
			defautlRegion: ['广东省', '东莞市', '东莞市'],
			locationName: '东莞市',
			cur_img_value: 1,
			chart: {
				day_list: ['04-27', '04-28', '04-29', '04-30'],
				max_temp_list: [29, 29, 29, 29],
				min_temp_list: [20, 19, 21, 21]
			},
			option: {},
			weather: {
				cur_temp: 30,
				max_temp: '33',
				min_temp: '26',
				water_value: '66',
				wind_unit: '西南风1级',
				wind_value: '',
				fur_day_wea: [
					{
						day_text_value: '今天',
						day_wea_icon: '../../static/images/weather_icons/weathercn/1.png',
						day_wea_text: '多云'
					},
					{
						day_text_value: '星期五',
						day_wea_icon: '../../static/images/weather_icons/weathercn/2.png',
						day_wea_text: '阴天'
					},
					{
						day_text_value: '星期六',
						day_wea_icon: '../../static/images/weather_icons/weathercn/3.png',
						day_wea_text: '阵雨'
					},
					{
						day_text_value: '星期天',
						day_wea_icon: '../../static/images/weather_icons/weathercn/4.png',
						day_wea_text: '雷阵雨'
					}
				],
				cur_desc: '',
				cur_wind_desc: '',
				next_wea_desc: ''
			}
		};
	},
	mounted() {
		let _this = this;
		_this.$nextTick(() => {
			// #ifdef MP-WEIXIN
			_this.$refs['canvas'].init();
			// #endif
		});
		// #ifdef APP-PLUS || H5
		_this.option = _this.setOption(_this.chart.day_list, _this.chart.max_temp_list, _this.chart.min_temp_list);
		// #endif
	},
	onLoad() {},
	onUnload() {
		let _this = this;
		// #ifdef MP-WEIXIN
		if (chartObject) {
			//释放charts
			chartObject.dispose();
			chartObject = null;
		}
		// #endif
	},
	onShow() {
		let _this = this;
		_this.getLocationBySDK();
	},
	methods: {
		/**
		 * 手动选择城市
		 */
		chooseCity(event) {
			let _this = this;
			_this.locationName = event.city.label;
			_this.defautlRegion = [event.province.label, event.city.label, event.area.label];
			_this.getWeatherByLocation(_this.locationName);
		},
		/**
		 * 通过第三方SDK获取定位
		 */
		getLocationBySDK() {
			let _this = this,
				amapFile = require('../../components/amap/amap-wx.js'),
				amapPlugin = new amapFile.AMapWX({
					key: '210e9c5f5f988e0aa466d869da3a9171'
				});
			amapPlugin.getPoiAround({
				success: function(data) {
					if (data.poisData[0]) {
						/**
						 * @param {Object} city --城市
						 * @param {Object} latitude --纬度
						 * @param {Object} longitude --经度
						 */
						let city = data.poisData[0].cityname,
							latitude = data.markers[0].latitude,
							longitude = data.markers[0].longitude;
						//当前定位城市
						_this.location = city;
						_this.regionStr = city;

						//请求天气数据
						_this.getWeatherByLocation(city);
					}
				},
				fail: function(info) {
					console.error('info', info);
					_this.getCurLocation();
				}
			});
		},
		/**
		 * 获取当前地理位置
		 */
		getCurLocation() {
			var _this = this;
			// uni自带定位s
			uni.getLocation({
				type: 'gcj02',
				address: true,
				geocode: true,
				success: function(res) {
					if (res) {
						/**
						 * @param {Object} city --城市
						 * @param {Object} latitude --纬度
						 * @param {Object} longitude --经度
						 */
						let city = res.address.city,
							latitude = res.latitude,
							longitude = res.longitude;
						//当前定位城市
						_this.location = city;
						//请求天气数据
						_this.getWeatherByLocation(city);
					}
				},
				fail: function(res) {
					console.error('res', res);
				},
				complete: () => {}
			});
		},
		/**
		 * 通过城市名 查询对应的天气信息
		 * @param {Object} cityName
		 */
		getWeatherByLocation(cityName) {
			let _this = this;
			_this.showLoading = true;
			let app_code = '5619664a9da343f0a87eea51ad4dad0d';
			uni.request({
				url: 'https://jisutqybmf.market.alicloudapi.com/weather/query',
				method: 'GET',
				data: {
					city: cityName
				},
				header: {
					Authorization: 'APPCODE ' + app_code //自定义请求头信息
				},
				timeout: 10000, //请求超时时间
				success: res => {
					if (res.data.status === 0) {
						new Promise((resolve, reject) => {
							setTimeout(() => {
								_this.showLoading = false;
								resolve();
							}, 2500);
						}).then(() => {
							let result = res.data.result,
								cur_wea_icon_value = parseInt(result.img),
								daily = result.daily; // --未来预报数组
							_this.weather.cur_temp = result.temp; // --当前温度
							_this.weather.cur_img_value = cur_wea_icon_value; // --当前天气图标
							_this.weather.max_temp = result.temphigh; // --最高温度
							_this.weather.min_temp = result.templow; // --最低温度
							_this.weather.water_value = result.humidity; // --湿度
							_this.weather.wind_unit = result.winddirect; // --风向
							_this.weather.wind_value = result.windpower; // --风速
							_this.weather.cur_desc = result.weather; // --实时天气
							_this.weather.cur_wind_desc = result.winddirect + result.windpower; // --实时风速
							/**
							 * day_list -- 未来四天预报数组
							 * date_list -- echarts配置项时间
							 * max_temp_list -- echarts配置项最高温度
							 * min_temp_list --echarts配置项最低温度
							 */
							let day_list = [],
								date_list = [],
								max_temp_list = [],
								min_temp_list = [],
								img_prefix = '../../static/images/weather_icons/weathercn/',
								img_endfix = '.png';
							//其他天气图标
							if (cur_wea_icon_value > 12) {
								_this.other_wea_icon = img_prefix + cur_wea_icon_value + img_endfix;
							}
							for (let index in daily) {
								if (index < 4) {
									let sin_day;
									let sin_date_name = daily[index].date.substr(5);
									date_list.push(sin_date_name);
									max_temp_list.push(daily[index].day.temphigh);
									min_temp_list.push(daily[index].night.templow);
									if (index == 0) {
										sin_day = {
											day_text_value: '今天',
											day_wea_icon: img_prefix + daily[index].day.img + img_endfix,
											day_wea_text: daily[index].day.weather
										};
									} else {
										sin_day = {
											day_text_value: daily[index].week,
											day_wea_icon: img_prefix + daily[index].day.img + img_endfix,
											day_wea_text: daily[index].day.weather
										};
									}
									day_list.push(sin_day);
								}
							}
							_this.weather.fur_day_wea = day_list;
							// #ifdef APP-PLUS || H5
							_this.option = _this.setOption(date_list, max_temp_list, min_temp_list);
							// #endif
							// #ifdef MP-WEIXIN
							_this.ec.option.xAxis.data = date_list;
							_this.ec.option.series[0].data = max_temp_list;
							_this.ec.option.series[1].data = min_temp_list;
							// #endif
						});
					}
				},
				fail: res => {
					_this.showLoading = false;
					uni.showToast({
						title: '服务器开小差了...',
						icon: 'none',
						mask: true,
						duration: 2000
					});
				}
			});
		},
		/**
		 * 点击显示内容详情
		 * @param {Object} event
		 */
		showDesc(event) {
			let toast_text = event.currentTarget.dataset.desc;
			uni.showToast({
				title: toast_text,
				icon: 'none'
			});
		},
		/**
		 * 微信小程序初始化图表
		 */
		initChart() {
			let _this = this,
				//获取像素比
				dpr = _this.getPixelRatio();
			chartObject = wxEcharts.init(canvas, null, {
				width: width,
				height: height,
				devicePixelRatio: dpr
			});
			canvas.setChart(chartObject);
			chartObject.setOption();
			return chartObject;
		},
		/**
		 * 获取微信小程序像素比
		 * @param {Object} value
		 */
		getPixelRatio: function() {
			let pixelRatio = 0;
			wx.getSystemInfo({
				success: function(res) {
					pixelRatio = res.pixelRatio;
				},
				fail: function() {
					pixelRatio = 0;
				}
			});
			return pixelRatio;
		},
		/**
		 * 设置echarts配置项
		 * @param {Object} day_list 日期
		 * @param {Object} max_temp_list 最高温
		 * @param {Object} min_temp_list 最低温
		 */
		setOption(day_list, max_temp_list, min_temp_list) {
			let that = this;
			let option = {
				xAxis: {
					type: 'category',
					axisTick: {
						//y轴刻度线
						show: false
					},
					boundaryGap: false,
					axisLine: {
						show: false
					},
					splitLine: {
						show: false //隐藏或显示
					},
					data: day_list
				},
				yAxis: {
					type: 'value',
					axisTick: {
						//y轴刻度线
						show: false
					},
					boundaryGap: false,
					axisLine: {
						show: false,
						onZero: false,
						lineStyle: {
							type: 'solid'
						}
					},
					axisLabel: { formatter: '{value} ℃' },
					splitLine: {
						show: false //隐藏或显示
					}
				},
				grid: {
					top: '10%',
					bottom: '10%',
					left: '12%',
					right: '12%'
				},
				tooltip: {
					trigger: 'axis',
					formatter: function(params, ticket, callback) {
						var tipString = params[0].axisValue + '\n' + params[0].seriesName + ':' + params[0].value + '℃' + '\n' + params[1].seriesName + ':' + params[1].value + '℃';
						return tipString;
					},
					/*设置x轴左右固定，上下跟随。*/
					position: function(point, params, dom, rect, size) {
						return [120, point[1]];
					}
				},
				color: ['#92DCC7', '#B2D494'],
				legend: {
					data: ['最高温', '最低温']
				},
				series: [
					{
						name: '最高气温',
						data: max_temp_list,
						type: 'line',
						symbol: 'none',
						itemStyle: {
							normal: {
								lineStyle: {
									color: '#92DCC7'
								}
							}
						}
					},
					{
						name: '最低气温',
						data: min_temp_list,
						symbol: 'none',
						type: 'line',
						itemStyle: {
							normal: {
								lineStyle: {
									color: '#B2D494'
								}
							}
						}
					}
				]
			};
			return option;
		},
		/**
		 * 排序
		 * @param arr
		 * @param type
		 */
		getMaxOrMin(arr, type) {
			if (type == 'max') {
				return Math.max.apply(Math, arr);
			} else if (type == 'min') {
				return Math.min.apply(Math, arr);
			}
		}
	}
};
</script>
<style>
page {
	background-color: rgb(70, 147, 155);
}
</style>
<style lang="scss" scoped>
@import url('../../static/style/weather.css');
/* #ifdef H5  */
.content {
	width: 100%;
	height: calc(100vh - 44px);
}
/* #endif */
.weather_icon {
	color: #fff;
	position: relative;

	.cur_temp {
		position: relative;
	}

	.cur_temp_value {
		font-size: 3rem;
	}

	.cur_temp_unit {
		position: absolute;
		font-size: 30px;
		top: -20rpx;
		right: -50rpx;
		font-weight: normal;
	}
}

.temp_info_area {
	margin: 5%;
	justify-content: space-between;
	.max_min_temp {
		align-items: center;
	}

	.temp_part {
		margin-top: 5%;
		margin-left: 5%;
		margin-right: 5%;
	}

	.temp_text_box {
		margin-left: 5px;
	}

	.top_fur_wea {
		padding-top: 6%;
		justify-content: space-around;
	}

	.sin_fur_wea {
		justify-content: center;
		align-items: center;
	}

	.day_wea_ico {
		margin-top: 10%;
	}

	.daywea_icon {
		width: 40px;
	}
	.top_icon {
		width: 50rpx;
	}
}

.feather-weather {
	padding: 30rpx 0px;
	background-color: #f5f5f5;
	justify-content: space-between;
	.top_fur_wea {
		margin: 0 5%;
		width: 90%;
		justify-content: space-between;
		.sin_fur_wea {
			justify-content: center;
			align-items: center;
		}

		.day_wea_ico {
			margin-top: 10%;
		}

		.daywea_icon {
			width: 40px;
		}
	}
}

.chart-area {
	width: 100%;
	height: 40vh;
	background-color: #f5f5f5;
	.uni-ec-canvas {
		padding: 10rpx 0rpx;
		width: 100%;
		height: 100%;
		display: block;
	}
}

.bottom-area{
	position: absolute;
	bottom: 0;
	width: 100%;
	height: 100rpx;
}

.roating {
	animation: rotate 2s linear infinite;
}
@-webkit-keyframes rotate {
	from {
		-webkit-transform: rotate(0deg);
	}

	to {
		-webkit-transform: rotate(360deg);
	}
}

@-moz-keyframes rotate {
	from {
		-moz-transform: rotate(0deg);
	}

	to {
		-moz-transform: rotate(359deg);
	}
}

@-o-keyframes rotate {
	from {
		-o-transform: rotate(0deg);
	}

	to {
		-o-transform: rotate(359deg);
	}
}

@keyframes rotate {
	from {
		transform: rotate(0deg);
	}

	to {
		transform: rotate(359deg);
	}
}
</style>
