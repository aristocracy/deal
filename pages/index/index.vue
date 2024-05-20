<template>
	<view class="content">
		<header class="header">
			<div class="return" @click="goback"><span>&lt;</span></div>
			<div class="title"><text>确认订单</text></div>
		</header>
		<main>
			<div class="address">
				<div class="addr">
					<p>xx省xx市xx区xx街道</p>
					<b>xxxxxxxxxx</b>
					<p>张三 15000000000</p>
				</div>
				<div class="chgaddr" @click="chgaddr"><span>&gt;</span></div>
			</div>
			<div class="good">
				<div class="store">
					<div class="avatar">
						
					</div>
					<text>xxxx专营店</text>
				</div>
				<div class="gdt">
					<div class="gimg">
						<image src="../../static/logo.png" mode=""></image>
					</div>
					<div class="ginfo">
						<b>【建议拍3件】心心相印心心相印心心相印心心相印</b>
						<p>xxx*1件</p>
						<p>现在付款，预计x月x日送达</p>
						<p class="limit">限购3件</p>
					</div>
				</div>
				<div class="tags">
					<div>7天无理由退货</div>
					<div>晚发即赔</div>
					<div>极速退款</div>
					<div>专属客服</div>
				</div>
				<div class="cpr">
					<span>￥{{price}}</span>
					<div class="counter">
						<div class="decend" @click="decend">-</div>
						<div class="count">{{count}}</div>
						<div class="ascend" @click="ascend">+</div>
					</div>
				</div>
				<div class="trsc dtc">
					<span>订单运费</span><span>包邮</span>
				</div>
				<div class="trsi dtc">
					<span class="trsii">运费险 <icon type="info" size="14" /></span><span>商家赠送，可抵x元退货运费</span>
				</div>
				<div class="pld dtc">
					<div>平台优惠 <span style="color:red;">xxxx</span></div>
					<div @click="ticket"><text style="color:red">-￥3.00</text><span> &gt;</span></div>
				</div>
				<div class="bzs dtc">
					<span>备注</span><div>无备注 &gt;</div>
				</div>
			</div>
			<div class="pnp">
				<div>号码保护<icon type="info" size="14" /> 隐藏收件人真实手机号，保护隐私</div>
				<label><checkbox value="cb" :checked="pripnp" />xxx</label>
			</div>
			<div class="pay">
				<div class="wechat"><div class="logo"></div><text>微信支付</text></div>
				<div><label><checkbox value="cb" :checked="pripnp" /></label></div>
			</div>
		</main>
		<footer>
			<div class="act">
				<div class="pris">￥{{price}}</div>
				<div><button @click="submitp">提交订单</button></div>
			</div>
		</footer>
	</view>
</template>

<script setup>
	import {ref,readonly,computed} from "vue";
	const prc= readonly(3.01);
	const count=ref(1);
	const pripnp=ref(true);
	const price=computed(()=>(prc*count.value).toFixed(2));
	const goback= () => {
		console.log("go back");
	};
	const chgaddr= () => {
		console.log("change addr");
	};
	const decend = () =>{
		if(count.value>0){
			count.value-=1;
		}
	};
	const ascend = () =>{
		count.value+=1;
	};
	const ticket = () =>{
		console.log("ticket");
	};
	const submitp=()=>{
		console.log("submitp");
	}
</script>

<style scoped>
	@import url("../../static/styles/index.css");
</style>
