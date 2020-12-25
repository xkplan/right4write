<script>
	export default {
		globalData: {
			/*
			data: {
				"2020/12/05": {
					totalSpend: "40.00",
					totalIncome: "0.00",
					list: [
						{
							id: 0,
							icon: "",
							name: "吃饭",
							note: "备注xxxx",
							type: "spned",
							money: "20.00"
						}
					]
				}
			},
			dataMap: {
				"0": {
					datetime: "2020/12/05",
					index: 0
				}
			}
			*/
			data: {},
			dataMap: {},
			autoIncrementId: 0,
			spendTypes: [{
					icon: "/static/type-icon/hamburger.svg",
					selectedIcon: "/static/icon-data-selected.png",
					name: "吃饭"
				},
				{
					icon: "/static/type-icon/hamburger.svg",
					selectedIcon: "/static/icon-data-selected.png",
					name: "游戏"
				},
				{
					icon: "/static/type-icon/hamburger.svg",
					selectedIcon: "/static/icon-data-selected.png",
					name: "娱乐"
				},
				{
					icon: "/static/type-icon/hamburger.svg",
					selectedIcon: "/static/icon-data-selected.png",
					name: "购物"
				}
			],
			incomeTypes: [{
					icon: "/static/icon-data.png",
					selectedIcon: "/static/icon-data-selected.png",
					name: "红包"
				},
				{
					icon: "/static/icon-data.png",
					selectedIcon: "/static/icon-data-selected.png",
					name: "利息"
				},
				{
					icon: "/static/icon-data.png",
					selectedIcon: "/static/icon-data-selected.png",
					name: "收款"
				}
			]
		},
		onLaunch: function() {
			console.log('App Launch');
			let res = uni.getStorageSync("data");
			if (res) {
				console.log("success load data.")
				console.log(JSON.stringify(res));
				this.globalData.data = res;
				for (let i in this.globalData.data) {
					console.log(i)
				}
				for (let day in this.globalData.data) {
					for (let i in this.globalData.data[day].list) {
						let item = this.globalData.data[day].list[i];
						this.globalData.autoIncrementId = Math.max(this.globalData.autoIncrementId, item.id) + 1;
						this.globalData.dataMap[item.id] = {
							datetime: day,
							index: i
						};
					}
				}
				console.log("当前自增ID为" + this.globalData.autoIncrementId);
			}
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style>
	/*每个页面公共css */
	.uni-picker-container {
		z-index: 999999;
	}
</style>
