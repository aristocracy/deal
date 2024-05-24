<template>
	<view class="address">
		<div class="addrs">
			<scroll-view scroll-y="true" class="scrview">
				<view  v-for="(item,index) in addrlist" :key="index" class="addrCard">
					<view class="adinfo adcom" @click="chooseAddr(item.id)">
						<div>
							<p>{{ item.zone }}</p>
							<p><b>{{ item.address }}</b></p>
							<p><span>{{ item.name }}</span> <span>{{ item.phone }}</span></p>
						</div>
						<div>
							<uni-icons
								type="checkmarkempty"
								color="red"
								size="24"
								v-if="item.checked"
							/>
						</div>
					</view>
					<view class="adct">
						<div>
							<label @click="setDefaultAddr(item.id)">
							<radio :value="index.toString()" :checked="item.isDefault" />
							<text :class="item.isDefault?'def':'nodef'">{{ item.isDefault?"已设置为默认":"设为默认" }}</text></label>
						</div>
						<div class="adact">
							<span @click="deleteAddr(item.id)">删除</span><span @click="copyAddr(item.id)">复制</span><span @click="modify(item.id)">修改</span>
						</div>
					</view>
				</view>
			</scroll-view>
		</div>
		<div class="addres">
			<button type="warn" @click="newAddress">+新建地址</button>
		</div>
	</view>
</template>

<script setup>
	import { ref,onBeforeMount,getCurrentInstance } from 'vue';

	const addrlist = ref([
		{
			id:"xxa",
			name:"张三",
			phone:"12345678901",
			zone:"广东省深圳市南山区",
			address:"广东省深圳市南山区",
			isDefault:true,
			checked:true
		},
		{
			id:"xxb",
			name:"李四",
			phone:"12345678901",
			zone:"广东省深圳市南山区",
			address:"广东省深圳市南山区",
			isDefault:false,
			checked:false
		},
		{
			id:"xxc",
			name:"王五",
			phone:"12345678901",
			zone:"广东省深圳市南山区",
			address:"广东省深圳市南山区",
			isDefault:false,
			checked:false
		},
	]);
	const {proxy} = getCurrentInstance();
	onBeforeMount(()=>{
		let addr = uni.getStorageSync("addrList");
		if (addr) {
			addrlist.value = addr;
		}
	});
	const setDefaultAddr = (id) => {
		for(let i in addrlist.value){
			addrlist.value[i]["isDefault"]=false;
			if(addrlist.value[i]["id"]==id){
				addrlist.value[i]["isDefault"]=true;
			}
		}
		uni.setStorageSync("addrList",addrlist.value);
	};
	const chooseAddr = (id) => {
		for(let i in addrlist.value){
			addrlist.value[i]["checked"]=false;
			if(addrlist.value[i]["id"]==id){
				addrlist.value[i]["checked"]=true;
				uni.setStorageSync("chooseAddr",addrlist.value[i]);
				let eventChannel = proxy.getOpenerEventChannel();
				eventChannel.emit("acceptDataFromAddressList",addrlist.value[i]);
			}
		}
		uni.setStorageSync("addrList",addrlist.value);
		uni.navigateBack({
			url:"../index/index"
		});
	};
	const deleteAddr = (id) =>{
		addrlist.value = addrlist.value.filter((i)=>i.id!==id);
	};
	const copyAddr = (id) => {
		let copyaddr=addrlist.value.find((i)=>i.id==id);
		if(copyaddr){
			uni.setClipboardData({
				data:`收货人：${copyaddr.name}，手机号码：${copyaddr.phone}，所在地区：${copyaddr.zone}，详细地址：${copyaddr.address}`,//要被复制的内容
				success:()=>{//复制成功的回调函数
				  uni.showToast({//提示
					title:'复制成功'
				  })
				}
			});
		}
	};
	const modify = (id) => {
		uni.navigateTo({
			url:`../newAddress/newAddress?id=${id}`,
			success: (res) => {
				res.eventChannel.emit('sendDataToNewAddress', id);
			},
			events:{
				receiveNewAddress:(data)=>{
					addrlist.value = data;
				}
			}
		})
	};
	const newAddress = () =>{
		uni.navigateTo({
			url:"../newAddress/newAddress",
			events:{
				acceptDataFromNewAddress:(data)=>{
					addrlist.value = data;
				}
			}
		});
	};
</script>

<style scoped lang="less">
	@import url("../../static/styles/address.less");
</style>
