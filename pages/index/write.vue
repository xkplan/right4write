<template>
	<view class="container">
		<view class="topBar" :animation="topBarSlideAnim">
			<view class="topBar-title">
				<text class="topBar-title-name">添加记账</text>
				<image class="topBar-title-fork" src="~@/static/index/fork.svg" mode="aspectFit" @click="closeBox" />
			</view>
			<view class="topBar-subBar">
				<text class="topBar-subBar-spend" :class="currentTab == 'spend' ? 'font-selected' : ''" v-on:click="switchTab('spend')">支出</text>
				<text class="topBar-subBar-income" :class="currentTab == 'income' ? 'font-selected' : ''" v-on:click="switchTab('income')">收入</text>
			</view>
			<view class="topBar-subBar-spend-underLine" v-if="currentTab == 'spend'"></view>
			<view class="topBar-subBar-income-underLine" v-if="currentTab == 'income'"></view>
		</view>

		<view class="type-list" :animation="typeListSlideAnim">
			<view class="type-list-item" v-for="type in typeList">
				<image class="type-list-item-icon" src="~@/static/icon-data-selected.png" :src="type.icon" />
				<text class="type-list-item-name">{{type.name}}</text>
			</view>
		</view>

		<view class="inbox" :animation="inboxSlideAnim">
			<view class="inbox-note">
				<picker class="inbox-note-datetime" mode="date" @change="inputDatetime">{{datetime}}</picker>
				<input class="inbox-note-content" placeholder="填写备注" :value="note" @input="inputNote" />
			</view>
			<input class="inbox-money" placeholder="输入金额" disabled="disabled" :value="money" />
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
	export default {
		data() {
			return {
				inboxSlideAnim: {},
				topBarSlideAnim: {},
				typeListSlideAnim: {},
				currentTab: 'spend',
				catagory: '吃饭',
				money: "",
				note: "",
				datetime: "2020/12/20",
				typeList: [],
				spendTypes: [{
						icon: "/static/type-icon/hamburger.svg",
						name: "吃饭"
					},
					{
						icon: "/static/icon-data-selected.png",
						name: "游戏"
					},
					{
						icon: "/static/icon-data-selected.png",
						name: "游戏"
					},
					{
						icon: "/static/icon-data-selected.png",
						name: "游戏"
					},
					{
						icon: "/static/icon-data-selected.png",
						name: "游戏"
					},
					{
						icon: "/static/icon-data-selected.png",
						name: "游戏"
					},
					{
						icon: "/static/icon-data-selected.png",
						name: "游戏"
					},
					{
						icon: "/static/icon-data-selected.png",
						name: "游戏"
					},
					{
						icon: "/static/icon-data-selected.png",
						name: "游戏"
					},
					{
						icon: "/static/icon-data-selected.png",
						name: "游戏"
					},
					{
						icon: "/static/icon-data-selected.png",
						name: "游戏"
					},
					{
						icon: "/static/icon-data-selected.png",
						name: "游戏"
					},
					{
						icon: "/static/icon-data-selected.png",
						name: "游戏"
					}
				],
				incomeTypes: [{
						icon: "/static/icon-list.png",
						name: "红包"
					},
					{
						icon: "/static/icon-data.png",
						name: "利息"
					},
					{
						icon: "/static/icon-data-selected.png",
						name: "利息"
					},
					{
						icon: "/static/icon-data.png",
						name: "利息"
					}
				],
				keyClicked: ''
			}
		},
		onLoad() {},
		methods: {
			refreshTypeList() {
				if (this.$data.currentTab == 'spend') {
					for (let i of this.$data.spendTypes) this.$data.typeList.push(i);
				} else
				if (this.$data.currentTab == 'income') {
					for (let i of this.$data.incomeTypes) this.$data.typeList.push(i);
				}
			},
			switchTab(name) {
				this.$data.currentTab = name;
				this.$data.typeList.length = 0;
				this.refreshTypeList();
			},
			inputDatetime(e) {
				let date = new Date(e.target.value);
				this.$data.datetime = date.getFullYear() + "/" + (date.getMonth() + 1) + "/" + date.getDate();
			},
			inputNote(e) {
				this.$data.note = e.target.value;
			},
			inputKey(ch) {
				//点击效果
				this.$data.keyClicked = ch;
				let that = this;
				setTimeout(function() {
					that.$data.keyClicked = '';
				}, 200);

				let im = this.$data.money;
				if (im.length > 32) return;
				if (ch == '.') {
					if (im.indexOf('.') == -1) {
						if (im.length == 0) {
							this.$data.money = '0.';
						} else this.$data.money = im + ch;
					}
				} else if (ch == 'b') {
					if (im.length > 0) {
						this.$data.money = im.slice(0, im.length - 1);
					}
				} else if (ch == 'ok') {
					let that = this;
					this.$emit('addNewRecord', {
						datetime: that.$data.datetime,
						note: that.$data.note,
						money: parseFloat(that.$data.money),
						type: that.$data.currentTab,
						catagory: that.$data.catagory
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
					this.$data.money = im;
				}
			},
			closeBox() {
				this.$emit('writeboxclose');
				let animation = uni.createAnimation({
					duration: 600,
					timingFunction: 'ease',
				});
				animation.bottom('-596rpx').step();
				this.$data.inboxSlideAnim = animation.export();

				animation.top('-269rpx').step();
				this.$data.topBarSlideAnim = animation.export();

				animation.top('100vh').step();
				this.$data.typeListSlideAnim = animation.export();
			}
		},
		mounted() {
			this.refreshTypeList();
			let animation = uni.createAnimation({
				duration: 600,
				timingFunction: 'ease'
			});
			animation.bottom(0).step();
			this.$data.inboxSlideAnim = animation.export();

			animation.top(0).step();
			this.$data.topBarSlideAnim = animation.export();

			animation = uni.createAnimation({
				duration: 600,
				timingFunction: 'ease'
			});
			animation.top('280rpx').step();
			this.$data.typeListSlideAnim = animation.export();
		}
	}
</script>

<style>
	@import url("~@/static/index/write.css");
	@import url("~@/static/common.css");

	page {
		background-color: #F5F6F9;
	}
</style>
