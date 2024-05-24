<template>
	<view class="newaddr">
		<view class="formwarp">	
			<uni-forms ref="addrForm" :model="formData" validate-trigger="blur">
				<uni-forms-item label="收货人" name="name">
					<uni-easyinput type="text" :focus="_name" placeholder="请输入收货人" v-model="formData.name" />
				</uni-forms-item>
				<uni-forms-item label="手机号" name="phone">
					<uni-easyinput type="number" :focus="_phone" maxlength="11" placeholder="请输入手机号" v-model="formData.phone" />
				</uni-forms-item>
				<uni-forms-item label="所在地区" name="zone">
					<uni-easyinput type="text" :focus="_zone" class="zone_" placeholder="选择省市区街道" @focus="addrpop" v-model="formData.zone" />
				</uni-forms-item>
				<uni-forms-item label="详细地址" name="address">
					<uni-easyinput type="textarea" :focus="_addr" placeholder-class="_addr" maxlength=300 placeholder="小区楼号 门牌号 村等" v-model="formData.address" />
				</uni-forms-item>
				<!--<view class="defaultaddr">--><uni-forms-item label="设置为默认地址" name="isDefault" class="defaultaddr">
					<switch :checked="formData.isDefault" @change="defaultChange"></switch>
				<!-- </view> --></uni-forms-item>
				<view class="savebtn">
					<button type="warn" @click="formSubmit">保存</button>
				</view>
			</uni-forms>
		</view>
		<pick-adress ref="pickadress" @areainfo="ainfo"></pick-adress>
	</view>
</template>

<script setup>
	import {ref,reactive, readonly,getCurrentInstance,onBeforeMount} from "vue";
	import { onReady,onLoad } from '@dcloudio/uni-app';
	import uniPopup from "@/uni_modules/uni-popup/components/uni-popup/uni-popup.vue";
	import pickAdress from '@/uni_modules/john-pickAddress/components/john-pickAddress/pickAddress.vue';
	
	const {proxy} = getCurrentInstance();
	const addrForm=ref();
	const pickadress=ref();
	const addrpopup=ref();
	const _name=ref(true);
	const rol=ref(true);
	const _phone=ref(false);
	const _zone=ref(false);
	const _addr=ref(false);
	const areaddr=ref({});
	const phoneNumberRegexp=readonly(/^(13[0-9]|14[01456879]|15[0-35-9]|16[2567]|17[0-8]|18[0-9]|19[0-35-9])\d{8}$/);
	const addRules={
		name:{
			rules:[
				{
					required:true,
					error_message:"请输入收货人",
				}
			],
			label:'收货人',
			validateTrigger:'submit'
		},
		phone:{
			rules:[
				{
					required:true,
					error_message:"请输入手机号",
				},{
					validateFunction:(rule,value,data,callback) =>{
						if (!phoneNumberRegexp.test(value)) {
							callback("请输入正确的手机号");
						}
						return true;
					}
				}
			],
			label:'手机号',
			validateTrigger:'submit'
		},
		zone:{
			rules:[
				{
					required:true,
					error_message:"请选择地区",
				}
			],
			label:'所在地区',
			validateTrigger:'submit'
		},
		address:{
			rules:[
				{
					required:true,
					error_message:"请输入详细地址",
				}
			],
			label:'详细地址',
			validateTrigger:'submit'
		},
		isDefault:{
			rules:[],
			label:"设置为默认地址",
			validateTrigger:'submit'
		}
	};
	let formData = reactive({
		id:"",
		name:"",
		phone:"",
		zone:"",
		address:"",
		isDefault:false,
		checked:false
	});
	onReady(()=>{
		addrForm.value.setRules(addRules);
		document.getElementsByClassName("zone_")[0].getElementsByTagName("input")[0].readOnly=true;
	});
	onLoad(()=>{
		 let eventChannel = proxy.getOpenerEventChannel();
		 eventChannel.on("sendDataToNewAddress",(data)=>{
			 let modaddr=uni.getStorageSync("addrList").find((i)=>i.id==data);
			 formData.id=modaddr.id;
			 formData.name=modaddr.name;
			 formData.phone=modaddr.phone;
			 formData.zone=modaddr.zone;
			 formData.address=modaddr.address;
			 formData.isDefault=modaddr.isDefault;
			 formData.checked=modaddr.checked;
		 });
	});
	const ainfo = (val) =>{
		areaddr.value=val;
		formData.zone = `${val.province.name} ${val.city.name} ${val.district.name}`;
	};
	const addrpop = () => {
		//addrpopup.value.open("bottom");
		proxy.$nextTick(()=>{
			pickadress.value.onOpen();
		});
	};
	const defaultChange = (e) => {
		formData.isDefault=e.detail.value;
	};
	const formSubmit = (e) =>{
		addrForm.value.validate().then((res)=>{
			let adrl=uni.getStorageSync("addrList");
			let _find = adrl.find((i)=>i.id==formData.id);
			let fin = adrl.indexOf(_find);
			if(_find){
				if(formData.isDefault){
					for(let k of adrl){
						if(k.id!=formData.id){
							k["isDefault"] = false;
						}
					}
				}
				adrl.splice(fin,1,formData);
			}else{
				formData.id=`addr_${Date.now()}`;
				for(let k of adrl){
					k["checked"] = false;
				}
				formData.checked = true;
				if(adrl){
					if(formData.isDefault){
						for(let k of adrl){
							k["isDefault"] = false;
						}
					}
					adrl.unshift(formData);
				}else {
					formData.isDefault=true;
					adrl=[formData];
				}
			}
			uni.setStorageSync("addrList",adrl);
			uni.navigateBack({
				url:"../address/index",
				success: () => {
					let eventChannel = proxy.getOpenerEventChannel();
					if(_find){
						eventChannel.emit("receiveNewAddress",adrl);
					}else{
						eventChannel.emit("acceptDataFromNewAddress",adrl);
					}
				}
			});
		}).catch((err)=>{
			_name.value=false;
			_phone.value=false;
			_zone.value=false;
			_addr.value=false;
			switch(err[0]["key"]){
				case "name":
					proxy.$nextTick(()=>{
						_name.value=true;
					});
					break;
				case "phone":
					proxy.$nextTick(()=>{
						_phone.value=true;
					});
					break;
				case "zone":
					proxy.$nextTick(()=>{
						_zone.value=true;
					});
					break;
				case "address":
					proxy.$nextTick(()=>{
						_addr.value=true;
					});
					break;
				default:
					proxy.$nextTick(()=>{
						_name.value=true;
					});
			};
		});
	};
</script>

<style scoped lang="less">
	@import url("../../static/styles/newAddress.less");
</style>
