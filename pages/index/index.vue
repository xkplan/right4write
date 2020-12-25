<template class="box">
	<view class="container" style="display: flex;" :class="{noScroll: isShowWriteBox}">
		<summaryBox :spend="currentMonthSpend.toFixed(2)" :income="currentMonthIncome.toFixed(2)"></summaryBox>

		<scroll-view class="list" :class="{noScroll: isShowWriteBox}">
			<view class="list-card" v-for="day in mirrorData">
				<view class="list-card-title">
					<view class="list-card-title-left">
						<view class="list-card-title-left-split"></view>
						<text class="list-card-title-left-name">{{day.datetime.slice(5)}}</text>
					</view>
					<view class="list-card-title-right">
						<text class="list-card-title-right-income">+{{day.totalIncome.toFixed(2)}}</text>
						<text class="list-card-title-right-spend">-{{day.totalSpend.toFixed(2)}}</text>
					</view>
				</view>
				<view class="list-card-item" v-for="(t, ti) in day.list" @click="clickItem(day.datetime, ti)">
					<image class="list-card-item-icon" :src="t.icon" mode="widthFix" />
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
				mirrorData: [
					{
						datetime: "2020/12/05",
						totalSpend: "40.00",
						totalIncome: "0.00",
						list: [
							{
								id: 0
								icon: "",//冗余字段
								name: "吃饭",
								note: "备注xxxx",
								type: "spned",
								money: "20.00",
								datetime: "2020/12/05"
							}
						]
					}
				]
				*/
				mirrorData: [],
				//从日期到mirrorData数组下标的映射
				mirrorDataMap: new Map(),
				//类别名映射
				spendTypeMap: new Map(),
				incomeTypeMap: new Map(),
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

			doSaveData() {
				uni.setStorage({
					key: 'data',
					data: getApp().globalData.data,
					success: function() {
						console.log('succed save data.');
					}
				});
			},
			doAddRecord(record, saveGlobal = true) { //添加一条记录，保证时间顺序
				if (saveGlobal) {
					getApp().globalData.data.push(record);
					this.doSaveData();
				}
				let dayRecords;
				if (this.mirrorDataMap.get(record.datetime) == undefined) { //不存在当天的统计数据则初始化
					let i = 0;
					for (; i < this.mirrorData.length; i++) {
						if (this.mirrorData[i].datetime < record.datetime) break;
					}
					dayRecords = {
						datetime: record.datetime,
						totalSpend: 0,
						totalIncome: 0,
						list: []
					};
					this.mirrorData.splice(i, 0, dayRecords);
					this.mirrorDataMap.set(record.datetime, i);
					i++;
					for (; i < this.mirrorData.length; i++) {
						this.mirrorDataMap.set(this.mirrorData[i].datetime, this.mirrorDataMap.get(this.mirrorData[i].datetime) + 1);
					}
				} else dayRecords = this.mirrorData[this.mirrorDataMap.get(record.datetime)];
				//将数据写入统计数据
				//冗余分类图标
				if (record.type == 'spend') {
					record.icon = this.spendTypeMap.get(record.catagory);
				} else if (record.type == 'income') {
					record.icon = this.incomeTypeMap.get(record.catagory);
				}
				dayRecords.list.push(record);
				if (record.type == 'spend') {
					dayRecords.totalSpend += record.money;

					let currentMonthPrefix = new Date().format("yyyy/MM/");
					if (record.datetime.slice(0, currentMonthPrefix.length) == currentMonthPrefix) {
						this.currentMonthSpend += record.money;
					}
				} else if (record.type == 'income') {
					dayRecords.totalIncome += record.money;
					this.currentMonthIncome += record.money;

					let currentMonthPrefix = new Date().format("yyyy/MM/");
					if (record.datetime.slice(0, currentMonthPrefix.length) == currentMonthPrefix) {
						this.currentMonthIncome += record.money;
					}
				}
			},
			doDeleteRecord(recordId, save = true) { //删除一条记录，如果删除后当天数据为空则清除当天数据
				let originData = getApp().globalData.data;
				let i = 0;
				for (; i < originData.length; i++) {
					if (originData[i].id == recordId) break;
				}
				if (i == originData.length) {
					console.log("不存在 ID 为 " + recordId + "的数据，删除失败！");
					return;
				}
				let record = originData.splice(i, 1)[0];
				if (save) this.doSaveData();
				console.log("删除记录：" + JSON.stringify(record));
				//删除缓存
				let dayRecords = this.mirrorData[this.mirrorDataMap.get(record.datetime)];
				i = 0;
				for (; i < dayRecords.list.length; i++) {
					if (dayRecords.list[i].id == recordId) {
						break;
					}
				}
				if (i == dayRecords.list.length) return;
				record = dayRecords.list.splice(i, 1)[0];
				if (record.type == 'spend') {
					dayRecords.totalSpend -= record.money;

					let currentMonthPrefix = new Date().format("yyyy/MM/");
					if (record.datetime.slice(0, currentMonthPrefix.length) == currentMonthPrefix) {
						this.currentMonthSpend -= record.money;
					}
				} else if (record.type == 'income') {
					dayRecords.totalIncome -= record.money;

					let currentMonthPrefix = new Date().format("yyyy/MM/");
					if (record.datetime.slice(0, currentMonthPrefix.length) == currentMonthPrefix) {
						this.currentMonthIncome -= record.money;
					}
				}

				if (dayRecords.list.length == 0) {
					//删除后清除当天统计数据
					this.mirrorData.splice(this.mirrorDataMap.get(record.datetime), 1);
					this.mirrorDataMap.delete(record.datetime);
				}
			},
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
				//写入分类缓存
				for (let i of getApp().globalData.spendTypes) {
					this.spendTypeMap.set(i.name, i.icon);
				}
				for (let i of getApp().globalData.incomeTypes) {
					this.incomeTypeMap.set(i.name, i.icon);
				}
				
				let currentMonthPrefix = new Date().format("yyyy/MM/");
				let originData = getApp().globalData.data;
				for (let record of originData) {
					//映射日期到数组下标
					this.doAddRecord(record, false);
				}
				
				console.log("列表页预处理数据完成");
			},
			confirmRecord(rec) {
				let record = {
					id: rec.id,
					catagory: rec.catagory,
					note: rec.note,
					type: rec.type,
					money: rec.money,
					datetime: rec.datetime
				};
				//判断是编辑还是新增
				if (rec.id) {
					//编辑
					this.doDeleteRecord(rec.id, false);
					this.doAddRecord(record);
					console.log("修改一条记录：" + JSON.stringify(this.item));
				} else {
					//添加
					record.id = getApp().globalData.autoIncrementId++;
					this.doAddRecord(record, true);
					console.log("添加一条记录：" + JSON.stringify(record));
				}
				this.closeWriteBox();
			},
			clickItem(day, index) {
				let dayData = this.mirrorData[this.mirrorDataMap.get(day)];
				if (!dayData) return;
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
				this.doDeleteRecord(this.editRecord.id);
				this.isShowEditBox = false;
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
