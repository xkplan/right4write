<template class="box">
	<view class="container" style="display: flex;">
		<view class="box-summary">
			<text class="title-summary">统计</text>
			<view class="summary-card">
				<view class="summary-card-title">
					<view class="summary-card-title-split"></view>
					<text class="summary-card-title-name">本月概览</text>
				</view>
				<view class="summary-card-money">
					<view class="summary-card-money-spend">
						<text class="summary-card-money-spend-title">支出</text>
						<text class="summary-card-money-spend-number">76.90</text>
					</view>
					<view class="summary-card-money-income">
						<text class="summary-card-money-income-title">收入</text>
						<text class="summary-card-money-income-number">0.90</text>
					</view>
				</view>
				<cover-image class="summary-card-bg" mode="aspectFit" src="../../static/index/summary-bg.png"></cover-image>
			</view>
		</view>
		
		<view class="list">
			
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'Hello'
			}
		},
		onLoad() {

		},
		methods: {

		}
	}
</script>

<style>
	page {
		background-color: #F5F6F9;
	}
	
	

	/* 统计框 */
	.box-summary {
		position: fixed;
		top: 29rpx;
		left: 33rpx;

		display: flex;
		flex-direction: column;
	}

	.title-summary {
		font-size: 63rpx;
		color: #3C424A;
	}

	.summary-card {
		margin-top: 17rpx;
		width: 683rpx;
		height: 258rpx;
		background: #FFFFFF;
		box-shadow: 0px -2px 15px 10px rgba(0, 0, 0, 0.02);
		border-radius: 17px;
		display: flex;
		flex-direction: column;
	}

	.summary-card-title {
		display: flex;
		flex-direction: row;
		align-items: center;
		margin-left: 24rpx;
		height: 52rpx;
		margin-top: 29rpx;
	}

	.summary-card-title-split {
		width: 8rpx;
		height: 26rpx;
		background: #FF9D72;
	}

	.summary-card-title-name {
		height: 52rpx;
		font-size: 38rpx;
		color: #3C424A;
		line-height: 52rpx;
		margin-left: 14rpx;
		font-weight: 600;
	}

	.summary-card-money {
		display: flex;
		flex-direction: row;
		align-items: center;
		margin-top: 22rpx;
	}

	.summary-card-money-spend, .summary-card-money-income {
		display: flex;
		flex-direction: column;
		width: 280rpx;
		margin-left: 24rpx;
	}

	.summary-card-money-spend-title, .summary-card-money-income-title {
		height: 34rpx;
		font-size: 25rpx;
		font-weight: 400;
		color: #3C424A;
		line-height: 34rpx;
	}
	
	.summary-card-money-spend-number{
		height: 61rpx;
		font-size: 44rpx;
		font-weight: 500;
		color: #FE5250;
		line-height: 61rpx;
		margin-top: 8rpx;
	}

	.summary-card-money-income-number{
		height: 61rpx;
		font-size: 44rpx;
		font-weight: 500;
		color: #3C424A;
		line-height: 61rpx;
		margin-top: 8rpx;
	}
	
	.summary-card-bg{
		position: absolute;
		right: 0;
		bottom: 0;
		width: 243rpx;
		height: 243rpx;
	}
	
	/* 列表 */
	.list{
		margin-top: 413rpx;
		height: 150rpx;
		width: 100vw;
		padding: 33rpx;
		background-color: #2C405A;
	}
</style>
