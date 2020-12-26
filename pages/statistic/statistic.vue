<template>
	<view class="container" style="display: flex;">
		<summaryBox :spend="currentMonthSpend.toFixed(2)" :income="currentMonthIncome.toFixed(2)"></summaryBox>

		<writeBox v-if="isShowWriteBox" class="write-box" @closeWriteBox="closeWriteBox()" @confirmRecord="confirmRecord"></writeBox>

		<tabBar @openWriteBox="openWriteBox" tab="statistic"></tabBar>

		<view class="list">
			<view class="list-card">
				<view class="list-card-title">
					<view class="list-card-title-left">
						<view class="list-card-title-left-split"></view>
						<text class="list-card-title-left-name">数据统计</text>
					</view>
					<canvas canvas-id="canvasColumn" id="canvasColumn" class="line-chart"></canvas>
				</view>
			</view>
			<view class="list-card">
				<view class="list-card-title">
					<view class="list-card-title-left">
						<view class="list-card-title-left-split"></view>
						<text class="list-card-title-left-name">支出项</text>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				currentMonthSpend: 0,
				currentMonthIncome: 0,
				isShowWriteBox: false
			}
		},
		onLoad() {},
		methods: {
			openWriteBox() {
				uni.showToast({
					title: "不支持的操作",
					icon: "none"
				})
			},
			closeWriteBox() {
				uni.showToast({
					title: "不支持的操作",
					icon: "none"
				})
			},
			confirmRecord() {
				uni.showToast({
					title: "不支持的操作",
					icon: "none"
				});
			}
		}
	}
</script>

<style>
	@import url("/static/index/index.css");
	@import url("/static/statistic/statistic.css");

	page {
		background-color: #F5F6F9;
	}
</style>
