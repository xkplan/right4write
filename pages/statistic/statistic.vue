<template>
	<view class="container" style="display: flex;">
		<summaryBox></summaryBox>

		<writeBox v-if="isShowWriteBox" class="write-box" @closeWriteBox="closeWriteBox()" @confirmRecord="confirmRecord"></writeBox>

		<tabBar @openWriteBox="openWriteBox" tab="statistic"></tabBar>

		<view class="list">
			<view class="list-card">
				<view class="list-card-title">
					<view class="list-card-title-split"></view>
					<text class="list-card-title-name">数据统计</text>
				</view>
				<view class="list-card-item" v-if="curMonthData.length > 0">
					<canvas canvas-id="lineChart" id="lineChart" class="line-chart"></canvas>
					<image :src="lineChartImg" style="width: 629rpx; height: 580rpx;"></image>
				</view>
				<view class="list-card-item" v-if="curMonthData.length == 0">
					<image src="/static/statistic/no-data-chart.svg" mode="aspectFit" style="width: 380rpx; height: 380rpx;"></image>
					<text style="margin-bottom: 64rpx; color: #3C424A;">暂无数据</text>
				</view>
			</view>
			<view class="list-card">
				<view class="list-card-title">
					<view class="list-card-title-split"></view>
					<text class="list-card-title-name">支出项</text>
				</view>
				<view class="list-card-item" v-if="spendPieChartData.series.length > 0">
					<canvas canvas-id="spendPieChart" id="spendPieChart" class="line-chart"></canvas>
					<image :src="spendPieChartImg" style="width: 629rpx; height: 580rpx;"></image>
				</view>
				<view class="list-card-item" v-if="spendPieChartData.series.length == 0">
					<image src="/static/statistic/no-data-chart.svg" mode="aspectFit" style="width: 380rpx; height: 380rpx;"></image>
					<text style="margin-bottom: 64rpx; color: #3C424A;">暂无数据</text>
				</view>
			</view>
			<view class="list-card">
				<view class="list-card-title">
					<view class="list-card-title-split"></view>
					<text class="list-card-title-name">收入项</text>
				</view>
				<view class="list-card-item" v-if="incomePieChartData.series.length > 0">
					<canvas canvas-id="incomePieChart" id="incomePieChart" class="line-chart"></canvas>
					<image :src="incomePieChartImg" style="width: 629rpx; height: 580rpx;"></image>
				</view>
				<view class="list-card-item" v-if="incomePieChartData.series.length == 0">
					<image src="/static/statistic/no-data-chart.svg" mode="aspectFit" style="width: 380rpx; height: 380rpx;"></image>
					<text style="margin-bottom: 64rpx; color: #3C424A;">暂无数据</text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	import uCharts from '../../js_sdk/u-charts/u-charts/u-charts.js';

	var lineChart;
	var spendPieChart;
	var incomePieChart;

	export default {
		data() {
			return {
				isShowWriteBox: false,
				curMonthData: [],
				// lineChart: undefined,
				lineChartData: undefined,
				lineChartImg: undefined,
				// spendPieChart: undefined,
				spendPieChartData: undefined,
				spendPieChartImg: undefined,
				// incomePieChart: undefined,
				incomePieChartData: undefined,
				incomePieChartImg: undefined,
				pageScrollTop: 0,
				chartWidth: 629,
				chartHeight: 580
			}
		},
		onLoad() {
			this.loadData();
			this.refreshLineChart();
			this.refreshIncomePieChart();
			this.refreshSpendPieChart();
		},
		methods: {
			refreshScrollTop() { // 刷新滚动条位置(解决滚动事件穿透的问题,在关闭记账组件时把页面滚动到打开时的位置)
				let that = this;
				let query = uni.createSelectorQuery().in(this);
				query.selectViewport('.list').fields({
					scrollOffset: true
				}, function(data) {
					that.pageScrollTop = data.scrollTop;
					that.pageScrollYoffset = that.pageScrollTop;
				}).exec();
			},
			openWriteBox() {
				this.refreshScrollTop();
				this.isShowWriteBox = true;
			},
			closeWriteBox() {
				let that = this;
				setTimeout(function() {
					that.isShowWriteBox = false;
				}, 600);

				console.log("reset scrollTop: " + this.pageScrollTop);
				this.pageScrollYoffset = this.pageScrollTop;
				uni.pageScrollTo({
					scrollTop: this.pageScrollTop,
					duration: 0
				});
			},
			confirmRecord(rec) {
				let record = {
					id: getApp().globalData.autoIncrementId++,
					catagory: rec.catagory,
					note: rec.note,
					type: rec.type,
					money: rec.money,
					datetime: rec.datetime
				};

				let cmp = new Date().format("yyyy/MM/");
				if (record.datetime.slice(0, cmp.length) != cmp) return;
				//更新折线图
				let index = parseInt(record.datetime.slice(cmp.length));
				if (!this.curMonthData[index]) {
					this.curMonthData[index] = {
						totalSpend: 0,
						totalIncome: 0,
						list: []
					}
				}
				let dayRecords = this.curMonthData[index];
				if (record.type == 'spend') {
					dayRecords.totalSpend += record.money;
					this.lineChartData.series[1].data[index] += record.money;
				} else if (record.type == 'income') {
					dayRecords.totalSpend += record.money;
					this.lineChartData.series[0].data[index] += record.money;
				};
				dayRecords.list.push(record);
				this.lineChart.updateData(this.lineChartData);
				//更新饼图
				if (record.type == 'spend') {
					if (!this.spendPieChart) this.refreshSpendPieChart();
					let i = 0;
					for (; i < this.spendPieChartData.series.length; i++) {
						if (this.spendPieChartData.series[i].name == record.catagory) break;
					}
					if (i == this.spendPieChartData.series.length) {
						this.spendPieChartData.series.push({
							name: record.catagory,
							data: 0
						});
					}
					this.spendPieChartData.series[i].data += record.money;
					this.spendPieChart.updateData(this.spendChartData);
				} else if (record.type == 'income') {
					let i = 0;
					for (; i < this.incomePieChartData.series.length; i++) {
						if (this.incomePieChartData.series[i].name == record.catagory) break;
					}
					if (i == this.incomePieChartData.series.length) {
						this.incomePieChartData.series.push({
							name: record.catagory,
							data: 0
						});
					}
					this.incomePieChartData.series[i].data += record.money;
					if (this.incomePieChart == undefined) this.refreshIncomePieChart();
					this.incomePieChart.updateData(this.incomeChartData);
				}
				//添加
				getApp().globalData.data.push(record);
				this.doSaveData();
				console.log("添加一条记录：" + JSON.stringify(record));
				this.closeWriteBox();
			},
			loadData() {
				let cmp = new Date().format("yyyy/MM/");
				for (let record of getApp().globalData.data) {
					if (record.datetime.slice(0, cmp.length) != cmp) continue;
					let index = parseInt(record.datetime.slice(cmp.length));
					if (!this.curMonthData[index]) {
						this.curMonthData[index] = {
							totalSpend: 0,
							totalIncome: 0,
							list: []
						}
					}
					let dayRecords = this.curMonthData[index];
					if (record.type == 'spend') {
						dayRecords.totalSpend += record.money;
					} else if (record.type == 'income') {
						dayRecords.totalIncome += record.money;
					} else continue;
					dayRecords.list.push(record);
				}
			},
			refreshLineChartImage() {
				let that = this;
				// setTimeout(function() {
				uni.canvasToTempFilePath({
					x: 0,
					y: 0,
					width: uni.upx2px(that.chartWidth),
					height: uni.upx2px(that.chartHeight),
					fileType: 'png',
					canvasId: 'lineChart',
					success: function(res) {
						that.lineChartImg = res.tempFilePath;
					}
				});
				// }, 50);
			},
			refreshSpendPieChartImage() {
				let that = this;
				// setTimeout(function() {
				uni.canvasToTempFilePath({
					x: 0,
					y: 0,
					width: uni.upx2px(that.chartWidth),
					height: uni.upx2px(that.chartHeight),
					fileType: 'png',
					canvasId: 'spendPieChart',
					success: function(res) {
						that.spendPieChartImg = res.tempFilePath;
					}
				});
				// }, 50);
			},
			refreshIncomePieChartImage() {
				let that = this;
				// setTimeout(function() {
				uni.canvasToTempFilePath({
					x: 0,
					y: 0,
					width: uni.upx2px(that.chartWidth),
					height: uni.upx2px(that.chartHeight),
					fileType: 'png',
					canvasId: 'incomePieChart',
					success: function(res) {
						that.incomePieChartImg = res.tempFilePath;
					}
				});
				// }, 50);
			},
			refreshLineChart() {
				this.lineChartData = {
					categories: [],
					series: [{
						"name": "收入",
						"data": [],
						"color": "#3C424A"
					}, {
						"name": "支出",
						"data": [],
						"color": "#FE5250"
					}]
				};

				let i = 0;
				this.lineChartData.categories[i++] = "1";
				let curDay = parseInt(new Date().format("dd"));
				for (; i <= curDay; i++) this.lineChartData.categories.push(i);
				for (; i < 30; i++) {
					this.lineChartData.categories.push(i % 5 != 0 ? "" : i);
				}
				this.lineChartData.categories[30] = "30";
				this.lineChartData.categories[31] = "";
				this.lineChartData.categories[32] = "";
				for (i = 0; i <= 31; i++) {
					if (!this.curMonthData[i]) {
						this.lineChartData.series[0].data.push(0);
						this.lineChartData.series[1].data.push(0);
						continue;
					}
					this.lineChartData.series[0].data.push(this.curMonthData[i].totalIncome);
					this.lineChartData.series[1].data.push(this.curMonthData[i].totalSpend);
				}

				this.lineChart = new uCharts({
					$this: this,
					canvasId: "lineChart",
					type: 'line',
					legend: true,
					fontSize: 11,
					background: '#FFFFFF',
					pixelRatio: 1,
					animation: false,
					dataPointShape: false,
					dataLabel: false,
					categories: this.lineChartData.categories,
					series: this.lineChartData.series,
					xAxis: {
						labelCount: 7,
						itemCount: 31,
						gridEval: 5,
						gridType: "dash"
					},
					yAxis: {
						disabled: false,
						axisLine: false,
						gridType: "dash",
						format(v) {
							return v + "元";
						}
					},
					width: uni.upx2px(this.chartWidth),
					height: uni.upx2px(this.chartHeight),
					extra: {
						column: {
							width: uni.upx2px(this.chartWidth) * 0.45 / this.lineChartData.categories.length
						},
						lineStyle: 'straight'
					}
				});
				let that = this;
				this.lineChart.addEventListener("renderComplete", function() {
					that.refreshLineChartImage();
				});

			},
			refreshIncomePieChart() {
				if (!this.incomePieChartData) {
					this.incomePieChartData = {
						series: []
					};
				}
				let incomeMap = new Map();

				for (let i of this.curMonthData) {
					if (!i) continue;
					for (let record of i.list) {
						if (record.type == 'income') {
							if (incomeMap.get(record.catagory) == undefined) {
								incomeMap.set(record.catagory, this.incomePieChartData.series.length);
								this.incomePieChartData.series.push({
									name: record.catagory,
									data: 0
								});
							}
							this.incomePieChartData.series[incomeMap.get(record.catagory)].data += record.money;
						}
					}
				}

				if (this.incomePieChartData.series.length > 0) {
					this.incomePieChart = new uCharts({
						$this: this,
						canvasId: "incomePieChart",
						type: 'pie',
						legend: true,
						fontSize: 11,
						background: '#FFFFFF',
						pixelRatio: 1,
						animation: false,
						dataPointShape: false,
						dataLabel: true,
						categories: this.incomePieChartData.categories,
						series: this.incomePieChartData.series,
						width: uni.upx2px(629),
						height: uni.upx2px(580),
						extra: {
							pie: {
								labelWidth: 10
							}
						}
					});

					let that = this;
					this.incomePieChart.addEventListener("renderComplete", function() {
						that.refreshIncomePieChartImage();
					});
				} else this.incomePieChart = undefined;
			},
			refreshSpendPieChart() {
				if (!this.spendPieChartData) {
					this.spendPieChartData = {
						series: []
					};
				}
				let spendMap = new Map();

				for (let i of this.curMonthData) {
					if (!i) continue;
					for (let record of i.list) {
						if (record.type == 'spend') {
							if (spendMap.get(record.catagory) == undefined) {
								spendMap.set(record.catagory, this.spendPieChartData.series.length);
								this.spendPieChartData.series.push({
									name: record.catagory,
									data: 0
								});
							}
							this.spendPieChartData.series[spendMap.get(record.catagory)].data += record.money;
						}
					}
				}

				if (this.spendPieChartData.series.length > 0) {
					this.spendPieChart = new uCharts({
						$this: this,
						canvasId: "spendPieChart",
						type: 'pie',
						legend: true,
						fontSize: 11,
						background: '#FFFFFF',
						pixelRatio: 1,
						animation: false,
						dataPointShape: false,
						dataLabel: true,
						categories: this.spendPieChartData.categories,
						series: this.spendPieChartData.series,
						width: uni.upx2px(629),
						height: uni.upx2px(580),
						extra: {
							pie: {
								labelWidth: 10
							}
						}
					});
					let that = this;
					this.spendPieChart.addEventListener("renderComplete", function() {
						that.refreshSpendPieChartImage();
					});
				} else this.spendPieChart = undefined;
			}
		}
	}
</script>

<style>
	@import url("/static/statistic/statistic.css");

	page {
		background-color: #F5F6F9;
	}
</style>
