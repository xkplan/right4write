<script>
	export default {
		globalData: {
			/*
			data: [
				{
					id: 0,
					icon: "",
					name: "吃饭",
					note: "备注xxxx",
					type: "spned",
					money: "20.00"
				}
			],
			dataMap: new Map()
			*/
			data: [],
			//从ID映射到data索引下标
			// dataMap: new Map(),
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
					let record = this.globalData.data[i];
					this.globalData.autoIncrementId = Math.max(this.globalData.autoIncrementId, record.id) + 1;
				}
				console.log("当前自增ID为" + this.globalData.autoIncrementId);
			}

			Date.prototype.format = function(fmt) {
				var o = {
					"M+": this.getMonth() + 1, //月份 
					"d+": this.getDate(), //日 
					"h+": this.getHours(), //小时 
					"m+": this.getMinutes(), //分 
					"s+": this.getSeconds(), //秒 
					"q+": Math.floor((this.getMonth() + 3) / 3), //季度 
					"S": this.getMilliseconds() //毫秒 
				};
				if (/(y+)/.test(fmt)) {
					fmt = fmt.replace(RegExp.$1, (this.getFullYear() + "").substr(4 - RegExp.$1.length));
				}
				for (var k in o) {
					if (new RegExp("(" + k + ")").test(fmt)) {
						fmt = fmt.replace(RegExp.$1, (RegExp.$1.length == 1) ? (o[k]) : (("00" + o[k]).substr(("" + o[k]).length)));
					}
				}
				return fmt;
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
