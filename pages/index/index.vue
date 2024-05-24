<template>
	<view class="content">
		<button type="primary" @click="navigateToDeal">跳转至订单页</button>
	</view>
</template>

<script setup>
	const navigateToDeal = () => {
		uni.navigateTo({
			url:"../deal/deal",
		});
	};
</script>

<style scoped>
	@import url("../../static/styles/index.css");
</style>
