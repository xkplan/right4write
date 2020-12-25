<template class="box">
	<view class="container" style="display: flex;" :class="{noScroll: isShowWriteBox}">
		<summaryBox :spend="currentMonthSpend.toFixed(2)" :income="currentMonthIncome.toFixed(2)"></summaryBox>

		<scroll-view class="list" :class="{noScroll: isShowWriteBox}">
			<view class="list-card" v-for="(day, dayName) in mirrorData" :key="dayName">
				<view class="list-card-title">
					<view class="list-card-title-left">
						<view class="list-card-title-left-split"></view>
						<text class="list-card-title-left-name">{{dayName.slice(5)}}</text>
					</view>
					<view class="list-card-title-right">
						<text class="list-card-title-right-income">+{{day.totalIncome.toFixed(2)}}</text>
						<text class="list-card-title-right-spend">-{{day.totalSpend.toFixed(2)}}</text>
					</view>
				</view>
				<view class="list-card-item" v-for="(t, ti) in day.list" @click="clickItem(dayName, ti)">
					<image class="list-card-item-icon" src="~@/static/logo.png" mode="widthFix" />
					<view class="list-card-item-detail">
						<view class="list-card-item-detail-split"></view>
						<view class="list-card-item-detail-wrap">
							<view class="list-card-item-detail-left">
								<text class="list-card-item-detail-left-name">{{t.catagory}}</text>
								<text class="list-card-item-detail-left-note">{{t.note}}</text>
							</view>
							<text v-if="t.type == 'spend'" class="list-card-item-detail-money" style="color: #FE5250;">-{{t.money.toFixed(2)}}</text>
							<text v-if="t.type == 'income'" class="list-card-item-detail-money" style="color: #3C424A;">+{{t.money.toFixed(2)}}</text>
						</view>
					</view>
				</view>
			</view>
		</scroll-view>

		<tabBar @openWriteBox="openWriteBox" tab="list"></tabBar>

		<view v-if="isShowWriteBox" class="write-box-mask" @scroll.prevent :animation="writeBoxMaskAnim">
			<view class="write-box-wrap">
				<writebox class="write-box" :editRecord="editRecord" @writeboxclose="closeWriteBox()" @confirmRecord="confirmRecord"></writebox>
			</view>
		</view>

		<editBox v-if="isShowEditBox" class="editBox" :editRecord="editRecord" @closeEditBox="closeEditBox"
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
				mirrorData: {},
				editRecord: undefined,
				currentMonthSpend: 0,
				currentMonthIncome: 0,
				isShowWriteBox: false,
				listScrollTop: 0,
				isShowEditBox: false
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

				//清除正在编辑的记录
				this.editRecord = undefined;
			},
			loadData() {
				// #ifdef H5
				// console.log("加载 H5 数据");
				// #endif
				// #ifndef H5

				// #endif

				let date = new Date();
				let currentMonthPrefix = date.getFullYear() + "/" + (date.getMonth() + 1) + "/";
				this.mirrorData = getApp().globalData.data;
				for (let key in this.mirrorData) {
					if (key.slice(0, currentMonthPrefix.length) == currentMonthPrefix) {
						this.$data.currentMonthSpend += this.mirrorData[key].totalSpend;
						this.$data.currentMonthIncome += this.mirrorData[key].totalIncome;
					}
				}
				console.log("列表页预处理数据完成");
			},
			confirmRecord(rec) {
				//如果没有今天的数据就初始化
				if (!this.mirrorData[rec.datetime]) {
					this.mirrorData[rec.datetime] = {
						totalSpend: 0,
						totalIncome: 0,
						list: []
					};
				}
				//判断是编辑还是新增
				console.log(JSON.stringify(rec))
				if (rec.id) {
					//编辑
					let info = getApp().globalData.dataMap[rec.id];
					let item = getApp().globalData.data[info.datetime].list[info.index];
					item.icon = rec.icon;
					item.catagory = rec.catagory;
					item.note = rec.note;
					item.type = rec.type;
					item.money = rec.money;
					item.datetime = rec.datetime;
					if (info.datetime != rec.datetime) {
						//更改日期
						item = this.mirrorData[info.datetime].list.splice(info.index, 1);
						console.log(JSON.stringify(info));
						console.log(JSON.stringify(item));
						// this.mirrorData[rec.datetime].list.push(item);
						// info.datetime = rec.datetime;
						// info.index = this.mirrorData[rec.datetime].list.length - 1;
					}
					console.log("修改一条记录：" + JSON.stringify(this.item));
				} else {
					//添加
					let item = this.mirrorData[rec.datetime];
					if (rec.type == 'spend') {
						item.totalSpend += rec.money;

						let date = new Date();
						let currentMonthPrefix = date.getFullYear() + "/" + (date.getMonth() + 1) + "/";
						if (rec.datetime.slice(0, currentMonthPrefix.length) == currentMonthPrefix) {
							this.$data.currentMonthSpend += rec.money;
						}
					} else if (rec.type == 'income') {
						item.totalIncome += rec.money;
						this.$data.currentMonthIncome += rec.money;

						let date = new Date();
						let currentMonthPrefix = date.getFullYear() + "/" + (date.getMonth() + 1) + "/";
						if (rec.datetime.slice(0, currentMonthPrefix.length) == currentMonthPrefix) {
							this.$data.currentMonthIncome += rec.money;
						}
					}
					let addRecord = {
						id: getApp().globalData.autoIncrementId++,
						icon: "",
						catagory: rec.catagory,
						note: rec.note,
						type: rec.type,
						money: rec.money,
						datetime: rec.datetime
					};
					item.list.push(addRecord);
					getApp().globalData.dataMap[addRecord.id] = {
						datetime: addRecord.datetime,
						index: item.list.length - 1
					};
					console.log("添加一条记录：" + JSON.stringify(addRecord));
				}
				uni.setStorageSync("data", this.mirrorData);
				this.closeWriteBox();
			},
			clickItem(day, index) {
				let dayData = this.mirrorData[day];
				if (!dayData) return;
				console.log(day + " " + JSON.stringify(dayData))
				this.editRecord = dayData.list[index];
				this.editRecord["datetime"] = day;
				if (this.editRecord.type == 'spend') {
					let tmp = getApp().globalData.spendTypes;
					for (let i of tmp) {
						if (i.name == this.editRecord.catagory) {
							this.editRecord["icon"] = i.icon;
							break;
						}
					}
				} else if (this.editRecord.type == 'income') {
					let tmp = getApp().globalData.incomeTypes;
					for (let i of tmp) {
						if (i.name == this.editRecord.catagory) {
							this.editRecord["icon"] = i.icon;
							break;
						}
					}
				}
				this.isShowEditBox = true;
			},
			openEditDetailBox() {
				this.isShowEditBox = false;
				this.openWriteBox();
			},
			closeEditBox() {
				this.isShowEditBox = false;
			},
			deleteItem() {
				//删除
				let info = getApp().globalData.dataMap[this.editRecord.id];
				this.mirrorData[info.datetime].list.splice(info.index, 1);
				if(this.mirrorData[info.datetime].list.length == 0){
					this.mirrorData.delete(info.datetime);
				}
				this.isShowEditBox = false;
				uni.setStorageSync("data", this.mirrorData);
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
