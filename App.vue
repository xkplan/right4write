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
				"name": "交通费",
				"icon": "/static/type-icon/1000.png",
				"selectedIcon": "/static/type-icon/1000-s.png"
			}, {
				"name": "住房",
				"icon": "/static/type-icon/1001.png",
				"selectedIcon": "/static/type-icon/1001-s.png"
			}, {
				"name": "公交",
				"icon": "/static/type-icon/1002.png",
				"selectedIcon": "/static/type-icon/1002-s.png"
			}, {
				"name": "其他",
				"icon": "/static/type-icon/1003.png",
				"selectedIcon": "/static/type-icon/1003-s.png"
			}, {
				"name": "办公",
				"icon": "/static/type-icon/1004.png",
				"selectedIcon": "/static/type-icon/1004-s.png"
			}, {
				"name": "医疗",
				"icon": "/static/type-icon/1005.png",
				"selectedIcon": "/static/type-icon/1005-s.png"
			}, {
				"name": "吃饭",
				"icon": "/static/type-icon/1006.png",
				"selectedIcon": "/static/type-icon/1006-s.png"
			}, {
				"name": "咖啡",
				"icon": "/static/type-icon/1007.png",
				"selectedIcon": "/static/type-icon/1007-s.png"
			}, {
				"name": "娱乐",
				"icon": "/static/type-icon/1008.png",
				"selectedIcon": "/static/type-icon/1008-s.png"
			}, {
				"name": "宠物",
				"icon": "/static/type-icon/1009.png",
				"selectedIcon": "/static/type-icon/1009-s.png"
			}, {
				"name": "小吃",
				"icon": "/static/type-icon/1010.png",
				"selectedIcon": "/static/type-icon/1010-s.png"
			}, {
				"name": "快递",
				"icon": "/static/type-icon/1011.png",
				"selectedIcon": "/static/type-icon/1011-s.png"
			}, {
				"name": "教育",
				"icon": "/static/type-icon/1012.png",
				"selectedIcon": "/static/type-icon/1012-s.png"
			}, {
				"name": "日用",
				"icon": "/static/type-icon/1013.png",
				"selectedIcon": "/static/type-icon/1013-s.png"
			}, {
				"name": "服饰",
				"icon": "/static/type-icon/1014.png",
				"selectedIcon": "/static/type-icon/1014-s.png"
			}, {
				"name": "水果",
				"icon": "/static/type-icon/1015.png",
				"selectedIcon": "/static/type-icon/1015-s.png"
			}, {
				"name": "烟酒",
				"icon": "/static/type-icon/1016.png",
				"selectedIcon": "/static/type-icon/1016-s.png"
			}, {
				"name": "维修",
				"icon": "/static/type-icon/1017.png",
				"selectedIcon": "/static/type-icon/1017-s.png"
			}, {
				"name": "药品",
				"icon": "/static/type-icon/1018.png",
				"selectedIcon": "/static/type-icon/1018-s.png"
			}, {
				"name": "购物",
				"icon": "/static/type-icon/1019.png",
				"selectedIcon": "/static/type-icon/1019-s.png"
			}, {
				"name": "超市",
				"icon": "/static/type-icon/1020.png",
				"selectedIcon": "/static/type-icon/1020-s.png"
			}, {
				"name": "运动",
				"icon": "/static/type-icon/1021.png",
				"selectedIcon": "/static/type-icon/1021-s.png"
			}, {
				"name": "飞机",
				"icon": "/static/type-icon/1022.png",
				"selectedIcon": "/static/type-icon/1022-s.png"
			}, {
				"name": "饮料",
				"icon": "/static/type-icon/1023.png",
				"selectedIcon": "/static/type-icon/1023-s.png"
			}],
			incomeTypes: [{
				"name": "其他",
				"icon": "/static/type-icon/2000.png",
				"selectedIcon": "/static/type-icon/2000-s.png"
			}, {
				"name": "兼职",
				"icon": "/static/type-icon/2001.png",
				"selectedIcon": "/static/type-icon/2001-s.png"
			}, {
				"name": "分红",
				"icon": "/static/type-icon/2002.png",
				"selectedIcon": "/static/type-icon/2002-s.png"
			}, {
				"name": "奖金",
				"icon": "/static/type-icon/2003.png",
				"selectedIcon": "/static/type-icon/2003-s.png"
			}, {
				"name": "工资",
				"icon": "/static/type-icon/2004.png",
				"selectedIcon": "/static/type-icon/2004-s.png"
			}, {
				"name": "现金",
				"icon": "/static/type-icon/2005.png",
				"selectedIcon": "/static/type-icon/2005-s.png"
			}, {
				"name": "理财",
				"icon": "/static/type-icon/2006.png",
				"selectedIcon": "/static/type-icon/2006-s.png"
			}, {
				"name": "退款",
				"icon": "/static/type-icon/2007.png",
				"selectedIcon": "/static/type-icon/2007-s.png"
			}, {
				"name": "销售",
				"icon": "/static/type-icon/2008.png",
				"selectedIcon": "/static/type-icon/2008-s.png"
			}]
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
