<template class="box">
	<view class="container" style="display: flex;" :class="{noScroll: isShowWriteBox}">
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
				<image class="summary-card-bg" mode="aspectFit" src="~@/static/index/summary-bg.png"></image>
			</view>
		</view>

		<view class="list" :class="{noScroll: isShowWriteBox}">
			<view class="list-card" v-for="day in data">
				<view class="list-card-title">
					<view class="list-card-title-left">
						<view class="list-card-title-left-split"></view>
						<text class="list-card-title-left-name">{{day.time}}</text>
					</view>
					<view class="list-card-title-right">
						<text class="list-card-title-right-income">+{{day.totalIncome}}</text>
						<text class="list-card-title-right-spend">-{{day.totalSpend}}</text>
					</view>
				</view>
				<view class="list-card-item" v-for="t in day.list">
					<image class="list-card-item-icon" src="~@/static/logo.png" mode="widthFix" />
					<view class="list-card-item-detail">
						<view class="list-card-item-detail-split"></view>
						<view class="list-card-item-detail-wrap">
							<view class="list-card-item-detail-left">
								<text class="list-card-item-detail-left-name">{{t.name}}</text>
								<text class="list-card-item-detail-left-note">{{t.note}}</text>
							</view>
							<text class="list-card-item-detail-money">{{t.money}}</text>
						</view>
					</view>
				</view>
			</view>
		</view>

		<view class="tabBar">
			<image src="~@/static/icon-list-selected.png" />
			<image src="~@/static/icon-data.png" />
			<image src="~@/static/icon-new.png" @click="openWriteBox()" />
		</view>

		<view v-if="isShowWriteBox" class="write-box-mask" @scroll.prevent :animation="writeBoxMaskAnim">
			<view class="write-box-wrap">
				<writebox class="write-box" style="z-index: 99999;" @writeboxclose="closeWriteBox()"></writebox>
			</view>
		</view>
	</view>
</template>

<script>
	import write from './write';

	export default {
		data() {
			return {
				writeBoxMaskAnim: {},
				data: [{
					time: "12/05",
					totalSpend: "40.00",
					totalIncome: "0.00",
					list: [{
							icon: "",
							name: "吃饭",
							note: "备注xxxx",
							type: "spned",
							money: "20.00"
						},
						{
							icon: "",
							name: "午餐",
							note: "备注xxxx",
							type: "spned",
							money: "20.00"
						}
					]
				}, {
					time: "12/05",
					totalSpend: "40.00",
					totalIncome: "0.00",
					list: [{
							icon: "",
							name: "吃饭",
							note: "备注xxxx",
							type: "spned",
							money: "20.00"
						},
						{
							icon: "",
							name: "午餐",
							note: "备注xxxx",
							type: "spned",
							money: "20.00"
						}
					]
				}, {
					time: "12/05",
					totalSpend: "40.00",
					totalIncome: "0.00",
					list: [{
							icon: "",
							name: "吃饭",
							note: "备注xxxx",
							type: "spned",
							money: "20.00"
						},
						{
							icon: "",
							name: "午餐",
							note: "备注xxxx",
							type: "spned",
							money: "20.00"
						}
					]
				}],
				isShowWriteBox: false
			}
		},
		onLoad() {
			this.closeWriteBox();
			// this.openWriteBox();
		},
		methods: {
			getScrollTop() { // 获取滚动条位置
				var scrollTop = 0;
				if (document.documentElement && document.documentElement.scrollTop) {
					scrollTop = document.documentElement.scrollTop;
				} else if (document.body) {
					scrollTop = document.body.scrollTop;
				}
				return scrollTop;
			},
			openWriteBox() {
				this.pageScrollYoffset = this.getScrollTop();
				this.isShowWriteBox = true;
				let animation = uni.createAnimation({
					duration: 600,
					timingFunction: 'ease',
				});
				animation.backgroundColor('#F5F6F9').step();
				this.$data.writeBoxMaskAnim = animation.export();
			},
			closeWriteBox() {
				let animation = uni.createAnimation({
					duration: 600,
					timingFunction: 'ease',
				});
				animation.opacity(0).step();
				this.$data.writeBoxMaskAnim = animation.export();

				let that = this;
				setTimeout(function() {
					that.isShowWriteBox = false;
				}, 600);
			}
		},
		components: {
			'writebox': write
		},
		watch: {
			isShowWriteBox(newVal, oldVal) {
				if (newVal == true) {
					let cssStr = "overflow-y: hidden; height: 100%;";
					document.getElementsByTagName('html')[0].style.cssText = cssStr;
					document.body.style.cssText = cssStr;
				} else {
					let cssStr = "overflow-y: auto; height: auto;";
					document.getElementsByTagName('html')[0].style.cssText = cssStr;
					document.body.style.cssText = cssStr;
				}

				// 下面需要这两行代码，兼容不同浏览器
				document.body.scrollTop = this.pageScrollYoffset;
				window.scroll(0, this.pageScrollYoffset);
			}
		}
	}
</script>

<style>
	@import url("~@/static/index/index.css");

	page {
		background-color: #F5F6F9;
	}
</style>
