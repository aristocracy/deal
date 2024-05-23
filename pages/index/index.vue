<template>
	<view class="content">
		<header class="header">
			<div class="return" @click="goback"><span><uni-icons type="left" size="25" /></span></div>
			<div class="title"><text>确认订单</text></div>
		</header>
		<main>
			<div class="address">
				<div class="addr">
					<template v-if="Object.keys(raddr).length>0">
						<p>{{raddr.zone}}</p>
						<b>{{raddr.address}}</b>
						<p>{{raddr.name}} {{raddr.phone}}</p>
					</template>
					<template v-else>
						<p>请设置一个收获地址</p>
					</template>
				</div>
				<div class="chgaddr" @click="chgaddr">
					<uni-icons type="right" size="25"></uni-icons>
				</div>
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
					<span class="trsii">运费险<uni-icons type="help" size="16" /></span><span>商家赠送，可抵x元退货运费</span>
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
				<div>号码保护<uni-icons type="help" size="16" /><span> 隐藏收件人真实手机号，保护隐私</span></div>
				<div>
					<label><checkbox @click="pnpt" value="protectPhoneNumber" :checked="pripnp" /></label>
				</div>
			</div>
			<div class="pay">
				<radio-group @change="payWay">
					<label class="payWayList" v-for="(item, index) in payType" :key="item.value">
						<view class="pict"><image :class="'payIcon '+item.value " :src="item.icon"></image><text>{{item.name}}</text></view>
						<view>
							<radio :value="item.value" :checked="index === payWaySelected" />
						</view>
					</label>
				</radio-group>
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
	import {ref,readonly,computed,onBeforeMount} from "vue";
	const prc= readonly(ref(3.01));
	const count=ref(1);
	const pripnp=ref(true);
	let raddr = ref({});
	const payType = ref([
		{
			name:"微信支付",
			value:"wechat",
			icon:"../../static/wechatpay.png",
		},
		{
			name:"支付宝",
			value:"alipay",
			icon:"../../static/alipay.png",
		},
	]);
	const payWaySelected = ref(0);
	const price=computed(()=>(prc.value*count.value).toFixed(2));
	onBeforeMount(()=>{
		let caddr=uni.getStorageSync("chooseAddr");
		if(caddr){
			raddr=ref(caddr);
		}
	});
	const goback= () => {
		console.log("go back");
	};
	const chgaddr= () => {
		uni.navigateTo({
			url:"/pages/address/index",
			animationType:"pop-in",
			animationDuration:1000,
			events:{
				acceptDataFromAddressList:(data)=>{
					raddr.value = data;
				}
			}
		});
	};
	const decend = () =>{
		if(count.value>1){
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
	};
	const pnpt = () => {
		pripnp.value=!pripnp.value;
	};
	const payWay = (evt)=> {
		for (let i = 0; i < payType.value.length; i++) {
			if (payType.value.value === evt.detail.value) {
				payWaySelected.value = i;
				break;
			}
		}
	};
</script>

<style scoped>
	@import url("../../static/styles/index.css");
</style>
