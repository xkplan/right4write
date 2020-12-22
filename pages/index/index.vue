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

		<scroll-view class="list" :class="{noScroll: isShowWriteBox}">
			<view class="list-card" v-for="(day, index) in data" :key="index">
				<view class="list-card-title">
					<view class="list-card-title-left">
						<view class="list-card-title-left-split"></view>
						<text class="list-card-title-left-name">{{index.slice(5)}}</text>
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
		</scroll-view>

		<view class="tabBar">
			<image src="~@/static/icon-list-selected.png" />
			<image src="~@/static/icon-data.png" />
			<image src="~@/static/icon-new.png" @click="openWriteBox()" />
		</view>

		<view v-if="isShowWriteBox" class="write-box-mask" @scroll.prevent :animation="writeBoxMaskAnim">
			<view class="write-box-wrap">
				<writebox class="write-box" @writeboxclose="closeWriteBox()" @addNewRecord="addNewRecord"></writebox>
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
				data: {},
				isShowWriteBox: false
			}
		},
		onLoad() {
			this.closeWriteBox();
			// this.openWriteBox();
			this.loadData();
		},
		methods: {
			getScrollTop() { // 获取滚动条位置
				var scrollTop = 0;
				// #ifdef H5
				console.log("h5")
				if (document.documentElement && document.documentElement.scrollTop) {
					scrollTop = document.documentElement.scrollTop;
				} else if (document.body) {
					scrollTop = document.body.scrollTop;
				}
				// #endif

				// #ifndef H5
				console.log("not h5");
				let query = uni.createSelectorQuery().in(this);
				query.selectViewport('.list').fields({
					scrollOffset: true
				}, function(data) {
					screenTop = data.scrollTop;
				}).exec();
				// #endif
				return scrollTop;
			},
			openWriteBox() {
				console.log(this.pageScrollYoffset);
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
			},
			loadData() {
				// #ifdef H5
				// console.log("加载 H5 数据");
				// #endif
				// #ifndef H5

				// #endif

				let that = this;
				uni.getStorage({
					key: "data",
					success: function(data) {
						console.log("success load data.")
						console.log(data.data);
						that.$data.data = data.data;
					}
				});
			},
			addNewRecord(data) {
				let store = this.$data.data;
				if (!store[data.datetime]) {
					store[data.datetime] = {
						totalSpend: 0,
						totalIncome: 0,
						list: []
					};
				}
				let item = store[data.datetime];
				if (data.type == 'spend') {
					item.totalSpend += data.money;
				} else if (data.type == 'income') {
					item.totalIncome += data.money;
				}
				item.list.push({
					icon: "",
					name: data.catagory,
					note: data.note,
					type: data.type,
					money: data.money
				});
				uni.setStorageSync("data", this.$data.data);
				this.closeWriteBox();
			}
		},
		components: {
			'writebox': write
		},
		watch: {
			isShowWriteBox(newVal, oldVal) {
				// #ifdef H5
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
				// #endif

				// #ifndef H5

				let query = uni.createSelectorQuery().in(this);
				query.selectViewport('html').fields({
					scrollOffset: true
				}, function(data) {
					console.log("得到布局位置信息" + JSON.stringify(data));
					console.log("节点离页面顶部的距离为" + data.scrollTop);
				}).exec();

				if (newVal == true) {
					let cssStr = "overflow-y: hidden; height: 100%;";
					document.getElementsByTagName('html')[0].style.cssText = cssStr;
					document.body.style.cssText = cssStr;
				} else {
					let cssStr = "overflow-y: auto; height: auto;";
					document.getElementsByTagName('html')[0].style.cssText = cssStr;
					document.body.style.cssText = cssStr;
				}
				// #endif
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
