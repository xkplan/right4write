<template>
	<view class="container" @click="closeBox">
		<view class="edit-box" @click.stop=''>
			<view class="edit-box-title">
				<text class="edit-box-title-name">{{record.catagory}}</text>
				<image class="edit-box-title-icon" :src="record.icon"></image>
			</view>
			<view class="edit-box-row">
				<text class="edit-box-row-name">金额</text>
				<text v-if="record.type == 'spend'" class="edit-box-row-data" style="color: #FE5250;">-{{record.money.toFixed(2)}}</text>
				<text v-if="record.type == 'income'" class="edit-box-row-data" style="color: #3C424A;">+{{record.money.toFixed(2)}}</text>
			</view>
			<view class="edit-box-row">
				<text class="edit-box-row-name">类型</text>
				<text class="edit-box-row-data" v-if="record.type == 'spend'">支出</text>
				<text class="edit-box-row-data" v-if="record.type == 'income'">收入</text>
			</view>
			<view class="edit-box-row">
				<text class="edit-box-row-name">日期</text>
				<text class="edit-box-row-data">{{record.datetime}}</text>
			</view>
			<input class="edit-box-note" :value="record.note" disabled="true" type="text" placeholder="无备注" placeholder-style="edit-box-note-placeholder" />
			<view class="edit-box-button">
				<text class="edit-box-button-delete" @click="deleteItem">删除</text>
				<text class="edit-box-button-edit" @click="openDetailBox">修改</text>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		props: ['editRecord'],
		data() {
			return {
				record: {
					type: 'spend',
					money: 0,
					note: "",
					datetime: "",
					catagory: '',
					icon: undefined,
					datetime: undefined
				}
			}
		},
		methods: {},
		methods: {
			closeBox() {
				this.$emit('closeEditBox');
			},
			openDetailBox() {
				this.$emit('openEditDetailBox');
			},
			deleteItem() {
				this.$emit('deleteItem');
			}
		},
		mounted() {
			if (this.editRecord) {
				this.record.type = this.editRecord.type;
				this.record.money = this.editRecord.money;
				this.record.catagory = this.editRecord.catagory;
				this.record.datetime = this.editRecord.datetime;
				this.record.note = this.editRecord.note;
				this.record.icon = this.editRecord.icon;
			}
		}
	}
</script>

<style>
	.container {
		height: 100vh;
		width: 100vw;
		background-color: rgba(0, 0, 0, 0.34);
		display: flex;
		justify-content: center;
		align-items: center;
	}

	/* 统计框 */
	.edit-box {
		width: 517rpx;
		height: 483rpx;
		display: flex;
		flex-direction: column;
		z-index: 99;
		background: #FFFFFF;
		border-radius: 40rpx;
		padding: 50rpx;
	}

	.edit-box-title {
		display: flex;
		justify-content: space-between;
	}

	.edit-box-title-name {
		height: 61rpx;
		font-size: 44rpx;
		font-weight: 500;
		color: #3C424A;
		line-height: 61rpx;
	}

	.edit-box-title-icon {
		width: 67rpx;
		height: 67rpx;
	}

	.edit-box-row {
		height: 42rpx;
		font-size: 29rpx;
		font-weight: 500;
		color: #3C424A;
		line-height: 42rpx;
		display: flex;
		justify-content: space-between;
		margin-top: 29rpx;
	}

	.edit-box-note {
		margin-top: 38rpx;
		width: 461rpx;
		height: 50rpx;
		background: #F1F5F9;
		border-radius: 27rpx;
		text-align: left;
		padding: 7rpx 28rpx;

		font-size: 25rpx;
		font-weight: 400;
		color: #3C424A;
		line-height: 34rpx;
	}

	.edit-box-note-placeholder {
		color: #BCC2CA;
	}

	.edit-box-button {
		width: 517rpx;
		height: 60rpx;
		display: flex;
		justify-content: space-between;
		text-align: center;
		margin-top: 45rpx;
	}

	.edit-box-button-delete {
		width: 235rpx;
		height: 60rpx;
		line-height: 60rpx;
		background: #F1F5F9;
		border-radius: 33rpx;

		font-size: 29rpx;
		font-weight: 500;
		color: #3C424A;
	}

	.edit-box-button-edit {
		width: 235rpx;
		height: 60rpx;
		line-height: 60rpx;
		background: #FF9D72;
		border-radius: 33rpx;

		font-size: 29rpx;
		font-weight: 500;
		color: #FFFFFF;
	}
</style>
