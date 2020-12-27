<template>
	<view class="writeBoxContainer" :animation="maskSlideAnim">
		<view class="topBar" :animation="topBarSlideAnim">
			<view class="topBar-title">
				<text class="topBar-title-name">添加记账</text>
				<image class="topBar-title-fork" src="~@/static/index/fork.svg" mode="aspectFit" @click="closeBox" />
			</view>
			<view class="topBar-subBar">
				<text class="topBar-subBar-spend" :class="record.type == 'spend' ? 'font-selected' : ''" v-on:click="switchTab('spend')">支出</text>
				<text class="topBar-subBar-income" :class="record.type == 'income' ? 'font-selected' : ''" v-on:click="switchTab('income')">收入</text>
			</view>
			<view class="topBar-subBar-spend-underLine" v-if="record.type == 'spend'"></view>
			<view class="topBar-subBar-income-underLine" v-if="record.type == 'income'"></view>
		</view>

		<scroll-view class="type-list-wrap" scroll-y="true">
			<view class="type-list" :animation="typeListSlideAnim">
				<view class="type-list-item" v-for="(type, index) in typeList" @click="select(index)">
					<image class="type-list-item-icon" :src="selected == index ? type.selectedIcon: type.icon" />
					<text class="type-list-item-name" :class="{'font-selected': selected == index}">{{type.name}}</text>
				</view>
			</view>
		</scroll-view>

		<view class="inbox" :animation="inboxSlideAnim">
			<view class="inbox-note">
				<picker class="inbox-note-datetime" mode="date" @change="inputDatetime">{{record.datetime}}</picker>
				<input class="inbox-note-content" placeholder="填写备注" placeholder-style="color: #BCC2CA" :value="record.note" @input="inputNote" />
			</view>
			<input class="inbox-money" placeholder="输入金额" disabled="disabled" :value="record.money" />
			<view class="inbox-keyboard">
				<view class="inbox-keyboard-row">
					<view class="inbox-keyboard-row-item" v-on:click="inputKey('1')" :class="keyClicked == '1' ? 'inbox-keyboard-row-item-selected' : ''">
						<text class="inbox-keyboard-row-item-text">1</text>
					</view>
					<view class="inbox-keyboard-row-item" v-on:click="inputKey('2')" :class="keyClicked == '2' ? 'inbox-keyboard-row-item-selected' : ''">
						<text class="inbox-keyboard-row-item-text">2</text>
					</view>
					<view class="inbox-keyboard-row-item" v-on:click="inputKey('3')" :class="keyClicked == '3' ? 'inbox-keyboard-row-item-selected' : ''">
						<text class="inbox-keyboard-row-item-text">3</text>
					</view>
					<view class="inbox-keyboard-row-item inbox-keyboard-row-item-backspace" :class="keyClicked == 'b' ? 'inbox-keyboard-row-item-backspace-selected' : ''"
					 v-on:click="inputKey('b')">
						<image class="inbox-keyboard-row-item-img" src="~@/static/index/left-arrow.svg" />
					</view>
				</view>
				<view class="inbox-keyboard-row">
					<view class="inbox-keyboard-row-item" v-on:click="inputKey('4')" :class="keyClicked == '4' ? 'inbox-keyboard-row-item-selected' : ''">
						<text class="inbox-keyboard-row-item-text">4</text>
					</view>
					<view class="inbox-keyboard-row-item" v-on:click="inputKey('5')" :class="keyClicked == '5' ? 'inbox-keyboard-row-item-selected' : ''">
						<text class="inbox-keyboard-row-item-text">5</text>
					</view>
					<view class="inbox-keyboard-row-item" v-on:click="inputKey('6')" :class="keyClicked == '6' ? 'inbox-keyboard-row-item-selected' : ''">
						<text class="inbox-keyboard-row-item-text">6</text>
					</view>
					<view class="inbox-keyboard-row-item inbox-keyboard-row-item-submit" :class="keyClicked == 'ok' ? 'inbox-keyboard-row-item-submit-selected' : ''"
					 v-on:click="inputKey('ok')">
						<image class="inbox-keyboard-row-item-img" src="~@/static/index/hook.svg" />
					</view>
				</view>
				<view class="inbox-keyboard-row">
					<view class="inbox-keyboard-row-item" v-on:click="inputKey('7')" :class="keyClicked == '7' ? 'inbox-keyboard-row-item-selected' : ''">
						<text class="inbox-keyboard-row-item-text">7</text>
					</view>
					<view class="inbox-keyboard-row-item" v-on:click="inputKey('8')" :class="keyClicked == '8' ? 'inbox-keyboard-row-item-selected' : ''">
						<text class="inbox-keyboard-row-item-text">8</text>
					</view>
					<view class="inbox-keyboard-row-item" v-on:click="inputKey('9')" :class="keyClicked == '9' ? 'inbox-keyboard-row-item-selected' : ''">
						<text class="inbox-keyboard-row-item-text">9</text>
					</view>
					<view class="inbox-keyboard-row-item" v-on:click="inputKey('')">
					</view>
				</view>
				<view class="inbox-keyboard-row">
					<view class="inbox-keyboard-row-item" v-on:click="inputKey('0')" :class="keyClicked == '0' ? 'inbox-keyboard-row-item-selected' : ''">
						<text class="inbox-keyboard-row-item-text">0</text>
					</view>
					<view class="inbox-keyboard-row-item" v-on:click="inputKey('00')" :class="keyClicked == '00' ? 'inbox-keyboard-row-item-selected' : ''">
						<text class="inbox-keyboard-row-item-text">00</text>
					</view>
					<view class="inbox-keyboard-row-item" v-on:click="inputKey('.')" :class="keyClicked == '.' ? 'inbox-keyboard-row-item-selected' : ''">
						<text class="inbox-keyboard-row-item-text">.</text>
					</view>
					<view class="inbox-keyboard-row-item">
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	/*
	record: {
		id: 0,
		type: 'spend',
		selected: 0,
		money: 0,
		note: "",
		datetime: "2020/12/20",
		catagory: ''
	}
	*/
	export default {
		props: ['editRecord'],
		data() {
			return {
				inboxSlideAnim: {},
				topBarSlideAnim: {},
				typeListSlideAnim: {},
				maskSlideAnim: {},
				selected: 0,
				record: {
					id: undefined,
					type: 'spend',
					money: "",
					note: "",
					datetime: "",
					catagory: ''
				},
				typeList: [],
				keyClicked: ''
			}
		},
		onLoad() {},
		methods: {
			refreshTypeList() {
				if (this.$data.record.type == 'spend') {
					for (let i of getApp().globalData.spendTypes) this.$data.typeList.push(i);
				} else
				if (this.$data.record.type == 'income') {
					for (let i of getApp().globalData.incomeTypes) this.$data.typeList.push(i);
				}
			},
			switchTab(name) {
				if (name != 'spend' && name != 'income') return;
				this.$data.record.type = name;
				this.$data.typeList.length = 0;
				this.$data.selected = 0;
				this.refreshTypeList();
				if (name == 'spend') {
					this.$data.record.catagory = getApp().globalData.spendTypes[0];
				} else if (name == 'income') {
					this.$data.record.catagory = getApp().globalData.incomeTypes[0];
				}
			},
			select(index) {
				this.$data.selected = index;
			},
			inputDatetime(e) {
				let date = new Date(e.target.value);
				this.record.datetime = date.format("yyyy/MM/dd");
			},
			inputNote(e) {
				this.record.note = e.target.value;
			},
			inputKey(ch) {
				//点击效果
				this.$data.keyClicked = ch;
				let that = this;
				setTimeout(function() {
					that.$data.keyClicked = '';
				}, 200);

				let im = this.record.money;
				if (im.length > 32) return;
				if (ch == '.') {
					if (im.indexOf('.') == -1) {
						if (im.length == 0) {
							this.record.money = '0.';
						} else this.record.money = im + ch;
					}
				} else if (ch == 'b') {
					if (im.length > 0) {
						this.record.money = im.slice(0, im.length - 1);
					}
				} else if (ch == 'ok') {
					if (!this.record.money) {
						uni.showToast({
							title: "请输入正确的金额",
							icon: "none"
						});
						return;
					}

					let that = this;
					let cc;
					if (this.record.type == 'spend') {
						cc = getApp().globalData.spendTypes[this.selected].name;
					} else if (this.record.type == 'income') {
						cc = getApp().globalData.incomeTypes[this.selected].name;
					}

					this.$emit('confirmRecord', {
						id: that.record.id,
						type: that.record.type,
						money: parseFloat(that.record.money),
						note: that.record.note,
						datetime: that.record.datetime,
						catagory: cc
					});
					this.closeBox();
				} else {
					if (ch == '00') {
						if (im.length == '0') ch = '0';
					}
					if (ch == '0') {
						if (im.length > 0 && im.charAt(0) == '0') return;
					} else if (ch == '00') {
						if (im.charAt(0) == '0') return;
					} else {
						if (im.length == 1 && im.charAt(0) == '0') {
							im = '';
						}
					}
					im = im + ch;
					if (im.indexOf('.') != -1 && im.length - im.indexOf('.') > 3) {
						im = im.slice(0, im.indexOf('.') + 3);
					}
					this.record.money = im;
				}
			},
			closeBox() {
				this.$emit('closeWriteBox');
				let animation = uni.createAnimation({
					duration: 600,
					timingFunction: 'ease',
				});
				animation.bottom('-596rpx').step();
				this.$data.inboxSlideAnim = animation.export();

				animation = uni.createAnimation({
					duration: 600,
					timingFunction: 'ease',
				});
				animation.top('-269rpx').step();
				this.$data.topBarSlideAnim = animation.export();

				animation = uni.createAnimation({
					duration: 600,
					timingFunction: 'ease',
				});
				animation.top('100vh').step();
				this.$data.typeListSlideAnim = animation.export();

				animation = uni.createAnimation({
					duration: 600,
					timingFunction: 'ease',
				});
				animation.opacity(0).step();
				this.maskSlideAnim = animation.export();
			}
		},
		mounted() {
			let date = new Date();
			this.$data.record.datetime = date.getFullYear() + "/" + (date.getMonth() + 1) + "/" + date.getDate();
			if (this.editRecord) {
				//修改
				this.$data.record.id = this.editRecord.id;
				this.$data.record.type = this.editRecord.type;
				this.$data.record.money = this.editRecord.money;
				this.$data.record.catagory = this.editRecord.catagory;
				this.$data.record.datetime = this.editRecord.datetime;
				this.$data.record.note = this.editRecord.note;
				let tmpList;
				if (this.record.type == 'spend') {
					tmpList = getApp().globalData.spendTypes;
				} else if (this.record.type == 'income') {
					tmpList = getApp().globalData.incomeTypes;
				}
				for (let i in tmpList) {
					if (tmpList[i].name == this.record.catagory) {
						this.selected = i;
						break;
					}
				}
			} else {
				//新建
				this.selected = 0;
				console.log("重置")
			}

			this.refreshTypeList();
			let animation = uni.createAnimation({
				duration: 600,
				timingFunction: 'ease'
			});
			animation.bottom(0).step();
			this.$data.inboxSlideAnim = animation.export();

			animation = uni.createAnimation({
				duration: 600,
				timingFunction: 'ease'
			});
			animation.top(0).step();
			this.$data.topBarSlideAnim = animation.export();

			animation = uni.createAnimation({
				duration: 600,
				timingFunction: 'ease'
			});
			// #ifdef APP-PLUS || APP-NVUE 
			animation.top('280rpx').step();
			// #endif
			// #ifdef MP-QQ || MP-WEIXIN || H5
			animation.top('225rpx').step();
			// #endif
			this.$data.typeListSlideAnim = animation.export();

			animation = uni.createAnimation({
				duration: 600,
				timingFunction: 'ease',
			});
			animation.backgroundColor('#F5F6F9').step();
			this.maskSlideAnim = animation.export();
		}
	}
</script>

<style>
	@import url("~@/static/common.css");

	page {
		background-color: #F5F6F9;
	}

	.writeBoxContainer {
		height: 100vh;
		width: 750rpx;
		top: 0;
		left: 0;
		background-color: rgba(245, 246, 249, 0);
		display: flex;
		align-items: center;
		z-index: 9999;

		position: fixed;
		justify-content: flex-start;
	}

	.topBar {
		width: 750rpx;
		height: 269rpx;
		/* #ifdef APP-PLUS || APP-NVUE */
		height: 269rpx;
		/* #endif */
		/* #ifdef MP-QQ || MP-WEIXIN || H5 */
		height: 214rpx;
		/* #endif */
		background: #FFFFFF;
		box-shadow: 0px -2rpx 15rpx 10rpx rgba(0, 0, 0, 0.02);
		border-radius: 0rpx 0rpx 43rpx 43rpx;
		display: flex;
		flex-direction: column;
		position: fixed;
		left: 0;
		top: -269rpx;
		z-index: 99;
	}

	.topBar-title {
		/* #ifdef APP-PLUS || APP-NVUE */
		margin-top: 79rpx;
		/* #endif */
		/* #ifdef MP-QQ || MP-WEIXIN || H5 */
		margin-top: 24rpx;
		/* #endif */
		margin-left: 33rpx;
		height: 88rpx;
		display: flex;
		flex-direction: row;
		align-items: center;
		position: relative;
	}

	.topBar-title-name {
		height: 88rpx;
		font-size: 63rpx;
		font-weight: 500;
		color: #3C424A;
		line-height: 88rpx;
		position: absolute;
		left: 0;
	}

	.topBar-title-fork {
		height: 33rpx;
		width: 33rpx;
		position: absolute;
		right: 60rpx;
	}

	.topBar-subBar {
		display: flex;
		flex-direction: row;
		justify-content: flex-start;
		margin-left: 33rpx;
		margin-top: 15rpx;
	}

	.topBar-subBar-spend {
		height: 47rpx;
		font-size: 33rpx;
		font-weight: 500;
		line-height: 47rpx;
	}

	.topBar-subBar-income {
		height: 47rpx;
		font-size: 33rpx;
		font-weight: 500;
		line-height: 47rpx;
		margin-left: 67rpx;
	}

	.topBar-subBar-spend-underLine {
		width: 60rpx;
		height: 8rpx;
		background: #FF9D72;
		border-radius: 4rpx;
		margin-left: 33rpx;
		margin-top: 6rpx;
		animation: all 1s;
	}

	.topBar-subBar-income-underLine {
		width: 60rpx;
		height: 8rpx;
		background: #FF9D72;
		border-radius: 4rpx;
		margin-left: 162rpx;
		margin-top: 6rpx;
		animation: all 1s;
	}

	/* 类型列表 */
	.type-list-wrap {
		width: 750rpx;
		height: 100vh;
	}

	.type-list {
		padding-bottom: 630rpx;
		width: 750rpx;
		display: flex;
		justify-content: flex-start;
		flex-wrap: wrap;
		position: absolute;
		top: 100vh;
		overflow: scroll;
	}

	.type-list-item {
		width: 196rpx;
		height: 75rpx;
		margin-left: 40.5rpx;
		background: #FFFFFF;
		box-shadow: 0rpx 0rpx 11rpx 2rpx rgba(0, 0, 0, 0.02);
		border-radius: 38rpx;
		display: flex;
		flex-direction: row;
		justify-content: flex-start;
		align-items: center;
		margin-top: 29rpx;
	}

	.type-list-item-icon {
		height: 67rpx;
		width: 67rpx;
		border-radius: 38rpx;
		margin-left: 6rpx;
	}

	.type-list-item-name {
		margin-left: 17rpx;
		height: 34rpx;
		font-size: 25rpx;
		font-weight: 500;
		color: #3C424A;
		line-height: 34rpx;
	}

	/* 输入框 */
	.inbox {
		width: 750rpx;
		height: 596rpx;
		background: #FFFFFF;
		box-shadow: 0rpx -2rpx 15rpx 10rpx rgba(0, 0, 0, 0.02);
		border-radius: 43rpx 43rpx 0rpx 0rpx;
		position: fixed;
		left: 0;
		bottom: -596rpx;
	}

	.inbox-note {
		height: 50rpx;
		margin: 33rpx 33rpx 0rpx 33rpx;
		display: flex;
		flex-direction: row;
	}

	.inbox-note-datetime {
		width: 179rpx;
		height: 54rpx;
		background: #FF9D72;
		border-radius: 27rpx;
		font-size: 25rpx;
		font-weight: 500;
		color: #FFFFFF;
		line-height: 50rpx;
		text-align: center;
	}

	.inbox-note-content {
		height: 54rpx;
		background: #F1F5F9;
		border-radius: 27rpx;
		margin-left: 18rpx;
		flex-grow: 1;
		padding: 0 24rpx;
		font-size: 25rpx;
		font-weight: 400;
		color: #3C424A;
		line-height: 34rpx;
	}

	.inbox-money {
		margin-left: 33rpx;
		margin-right: 33rpx;
		margin-top: 12rpx;
		height: 67rpx;
		background: #F1F5F9;
		border-radius: 27rpx;
		padding: 0 33rpx;
		text-align: right;
	}

	/* 键盘 */
	.inbox-keyboard {}

	.inbox-keyboard-row {
		height: 75rpx;
		display: flex;
		justify-content: space-evenly;
		align-items: center;
		margin-top: 25rpx;
	}

	.inbox-keyboard-row-item {
		height: 75rpx;
		width: 75rpx;
		border-radius: 37.5rpx;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.inbox-keyboard-row-item-text {
		text-align: center;
		font-size: 48rpx;
		font-weight: 500;
		color: #3C424A;
	}

	.inbox-keyboard-row-item-img {
		height: 30rpx;
		width: 30rpx;
	}

	.inbox-keyboard-row-item-selected {
		background: #F1F5F8;
	}

	.inbox-keyboard-row-item-backspace {
		background: #f1f5f8;
	}

	.inbox-keyboard-row-item-backspace-selected {
		background: #FF9D72;
	}

	.inbox-keyboard-row-item-submit {
		background: #FF9D72;
	}

	.inbox-keyboard-row-item-submit-selected {
		background: #eb875c;
	}
</style>
