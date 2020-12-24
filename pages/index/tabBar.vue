<template>
	<view class="tabBar">
		<image :src="tab == 'list' ? '/static/icon-list-selected.png' : '/static/icon-list.png'" />
		<image src="/static/icon-new.png" @click="openWriteBox" />
		<image :src="tab == 'chart' ? '/static/icon-data-selected.png' : '/static/icon-data.png'" />
	</view>
</template>

<script>
	export default {
		props: ['tab'],
		methods: {
			openWriteBox(){
				this.$emit('openWriteBox')
			}
		}
	}
</script>

<style>
	/* 底部导航栏 */
	.tabBar {
		height: 104rpx;
		width: 750rpx;
		background-color: #FFFFFF;
		position: fixed;
		bottom: 0;
		left: 0;
		display: flex;
		justify-content: space-evenly;
		align-items: center;
	}

	.tabBar image {
		height: 67rpx;
		width: 67rpx;
	}
</style>
