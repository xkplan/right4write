<template>
	<view class="container" @click="closeBox">
		<view class="edit-box" @click.prevent=''>
			<view class="edit-box-title">
				<text class="edit-box-title-name">{{item.name}}</text>
				<image class="edit-box-title-icon" :src="item.icon"></image>
			</view>
			<view class="edit-box-row">
				<text class="edit-box-row-name">金额</text>
				<text v-if="item.type == 'spend'" class="edit-box-row-data" style="color: #FE5250;">-{{item.money}}</text>
				<text v-if="item.type == 'income'" class="edit-box-row-data" style="color: #3C424A;">+{{item.money}}</text>
			</view>
			<view class="edit-box-row">
				<text class="edit-box-row-name">类型</text>
				<text class="edit-box-row-data" v-if="item.type == 'spend'">支出</text>
				<text class="edit-box-row-data" v-if="item.type == 'income'">收入</text>
			</view>
			<view class="edit-box-row">
				<text class="edit-box-row-name">日期</text>
				<text class="edit-box-row-data">{{datetime}}</text>
			</view>
			<input class="edit-box-note" :value="item.note" type="text" placeholder="无备注" placeholder-style="edit-box-note-placeholder" />
			<view class="edit-box-button">
				<text class="edit-box-button-delete" @click="deleteItem">删除</text>
				<text class="edit-box-button-edit" @click="openDetailBox">修改</text>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		props: ['item', 'datetime'],
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
