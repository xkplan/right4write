<template class="box">
	<view class="container" style="display: flex;" :class="{noScroll: isShowWriteBox}">
		<summaryBox :spend="currentMonthSpend" :income="currentMonthIncome"></summaryBox>

		<scroll-view class="list" :class="{noScroll: isShowWriteBox}">
			<view class="list-card" v-for="(day, dayName) in data" :key="dayName">
				<view class="list-card-title">
					<view class="list-card-title-left">
						<view class="list-card-title-left-split"></view>
						<text class="list-card-title-left-name">{{dayName.slice(5)}}</text>
					</view>
					<view class="list-card-title-right">
						<text class="list-card-title-right-income">+{{day.totalIncome}}</text>
						<text class="list-card-title-right-spend">-{{day.totalSpend}}</text>
					</view>
				</view>
				<view class="list-card-item" v-for="(t, ti) in day.list" @click="clickItem(dayName, ti)">
					<image class="list-card-item-icon" src="~@/static/logo.png" mode="widthFix" />
					<view class="list-card-item-detail">
						<view class="list-card-item-detail-split"></view>
						<view class="list-card-item-detail-wrap">
							<view class="list-card-item-detail-left">
								<text class="list-card-item-detail-left-name">{{t.name}}</text>
								<text class="list-card-item-detail-left-note">{{t.note}}</text>
							</view>
							<text v-if="t.type == 'spend'" class="list-card-item-detail-money" style="color: #FE5250;">-{{t.money}}</text>
							<text v-if="t.type == 'income'" class="list-card-item-detail-money" style="color: #3C424A;">+{{t.money}}</text>
						</view>
					</view>
				</view>
			</view>
		</scroll-view>

		<tabBar @openWriteBox="openWriteBox" tab="list"></tabBar>

		<view v-if="isShowWriteBox" class="write-box-mask" @scroll.prevent :animation="writeBoxMaskAnim">
			<view class="write-box-wrap">
				<writebox class="write-box" @writeboxclose="closeWriteBox()" @addNewRecord="addNewRecord"></writebox>
			</view>
		</view>

		<editBox v-if="isShowEditBox" class="editBox" :item="editItem" :datetime="editDatetime" @closeEditBox="closeEditBox"
		 @openEditDetailBox="openEditDetailBox" @deleteItem="deleteItem"></editBox>
	</view>
</template>

<script>
	import write from './write';
	import tabBar from './tabBar';
	import summaryBox from './summaryBox';
	import editBox from './editBox';

	export default {
		data() {
			return {
				writeBoxMaskAnim: {},
				/*
				data: {
					"2020/12/05": {
						totalSpend: "40.00",
						totalIncome: "0.00",
						list: [{
								icon: "",
								name: "吃饭",
								note: "备注xxxx",
								type: "spned",
								money: "20.00"
							}
						]
					}
				}
				*/
				data: {},
				currentMonthSpend: 0,
				currentMonthIncome: 0,
				isShowWriteBox: false,
				listScrollTop: 0,
				isShowEditBox: false,
				editItem: {},
				editDatetime: "2020/02/02",
				editItemIndex: undefined
			}
		},
		onLoad() {
			this.closeWriteBox();
			this.loadData();
		},
		methods: {
			refreshScrollTop() { // 刷新滚动条位置(解决滚动事件穿透的问题,在关闭记账组件时把页面滚动到打开时的位置)
				// #ifdef H5
				console.log("h5");
				let scrollTop = 0;
				if (document.documentElement && document.documentElement.scrollTop) {
					scrollTop = document.documentElement.scrollTop;
				} else if (document.body) {
					scrollTop = document.body.scrollTop;
				}
				this.$data.listScrollTop = scrollTop;
				this.pageScrollYoffset = this.$data.listScrollTop;
				// #endif

				// #ifndef H5
				console.log("not h5");
				let that = this;
				let query = uni.createSelectorQuery().in(this);
				query.selectViewport('.list').fields({
					scrollOffset: true
				}, function(data) {
					that.$data.listScrollTop = data.scrollTop;
					that.pageScrollYoffset = that.$data.listScrollTop;
				}).exec();
				// #endif
			},
			openWriteBox() {
				this.refreshScrollTop();

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

				console.log("reset scrollTop: " + this.$data.listScrollTop);
				this.pageScrollYoffset = this.$data.listScrollTop;
				uni.pageScrollTo({
					scrollTop: that.$data.listScrollTop,
					duration: 0
				});
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
						that.$data.data = getApp().globalData.data;
						that.$data.data = data.data;

						let date = new Date();
						let currentMonthPrefix = date.getFullYear() + "/" + (date.getMonth() + 1) + "/";
						for (let key in that.$data.data) {
							if (key.slice(0, currentMonthPrefix.length) == currentMonthPrefix) {
								console.log(JSON.stringify(that.$data.data[key]))
								that.$data.currentMonthSpend += that.$data.data[key].totalSpend;
								that.$data.currentMonthIncome += that.$data.data[key].totalIncome;
							}
						}
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
					this.$data.currentMonthSpend += data.money;
				} else if (data.type == 'income') {
					item.totalIncome += data.money;
					this.$data.currentMonthIncome += data.money;
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
			},
			clickItem(day, index) {
				let dayData = this.$data.data[day];
				if (!dayData) return;
				console.log(day + " " + JSON.stringify(dayData))
				this.$data.editItem = dayData.list[index];
				this.$data.editDatetime = day;
				this.$data.isShowEditBox = true;
				this.$data.editItemIndex = index;
			},
			openEditDetailBox() {

			},
			closeEditBox() {
				this.$data.isShowEditBox = false;
			},
			deleteItem() {

			}
		},
		components: {
			'writebox': write,
			'tabBar': tabBar,
			'summaryBox': summaryBox,
			'editBox': editBox
		},
		watch: {
			isShowWriteBox(newVal, oldVal) {}
		}
	}
</script>

<style>
	@import url("~@/static/index/index.css");

	page {
		background-color: #F5F6F9;
	}
</style>
