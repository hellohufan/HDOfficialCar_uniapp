<template>
	<view class="topBackView">
		<view class="section">
			<view class="alignment_justify">
				<view>
					<text>当前节点：</text>
					<text style="color: #f4643e;" v-if="curStatus.statusDesc">{{curStatus.statusDesc}}</text>
				</view>
				<text style="color: #707070;" class="overflowSuspension">维修单号：{{this.repairList.id}}</text>
			</view>
			<view class="uni-list">
				<block v-for="(item, index) in this.logs" :key="index">
					<view class="uni-list-cell" @click="goDetail(item)"> 
						<view class="alignment_left">
							<image class="schedule_icon"></image>
							<view class="schedule_time">{{item.dealTime}}</view>
						</view>
						<view class="alignment_left">
							<view class="schedule_line"></view>
							<text>{{item.statusDesc}}</text>
						</view>
					</view>
				</block>
			</view>
		</view>
		<view class="section">
			<view class="title_left">维修单详情</view>
			<view class="alignment_left">
				<view class="lable" v-if="repairList">{{repairList.cnName}}</view>
				<view class="lable" v-if="repairList">{{repairList.carType}}</view>
				<view class="lable backColorYellow" v-if="repairTypeName">{{repairTypeName}}</view>
			</view>
			<view class="line_seperate"></view>
			<view class="alignment_left">
				<text class="alignment_left margin_left_section">送修时间:</text>
				<view class="schedule_time">{{repairList.repairTime}}</view>
			</view>
			<view class="corner_gray_back">
				<view class="alignment_left margin_left">
					<text class="">维修厂：</text>
					<view class="" v-if="repairList">{{repairList.maintenanceName}}</view>
				</view>
				<view class="line_seperate"></view>
				<block v-for="(data, index) in this.maitainList" :key= "index">
					<view class="alignment_left">
						<view class="row_title margin_left">{{data.title}}</view>
						<view class="row_title">{{data.value}}</view>
					</view>
				</block>
			</view>
			<view class="remark">备注信息：{{repairList.remark}}</view>
		</view>
		<view class="section" style="padding-bottom: 60rpx;">
			<view class="title_left">材料费详情</view>
			<view class="line_table_seperate_first"></view>
			<view class="table_margin">
				<scroll-view style="padding-bottom: 0rpx;" class="scroll-view_H" scroll-x="true"  @scroll="scroll">
					<block v-for="(title, index) in materialFeeListTitle" :key="index">
						<view class="title_table">{{title}}</view>
						<block v-for="item in materialFeeList[index]">
							<view class="scroll-view-item_H table_border">{{item}}</view>
						</block>
						<!-- <view class="scroll-view-item_H uni-bg-red">A</view>
						<view class="scroll-view-item_H uni-bg-green">B</view>
						<view class="scroll-view-item_H uni-bg-blue">C</view>
						<view class=" scroll-view-item_H uni-bg-red">d</view>-->
						<view class="line_table_seperate_ampty"></view>
					</block>
					<view class="table_totle_justify">
						<view class="title_table">材料金额总计</view>
						<view class="table_totle_value_border">￥{{this.totleMateriaFee}}</view>
					</view>
				</scroll-view>
			</view>	
			<view class="line_table_seperate_last"></view>
		</view>
		<view class="section" style="padding-bottom: 60rpx;">
			<view class="title_left">工时费详情</view>
			<view class="line_table_seperate_first"></view>
			<view class="table_margin">
				<block v-for="(title, index) in workCostListTitles" :key="index">
					<view class="uni-flex uni-row">
						<view class="title_table">{{title}}</view>
						<view class="table_value_item table_border">C</view>
					</view>
					<view class="line_table_seperate_ampty"></view>
				</block>
			</view>
			<view class="line_table_seperate_last"></view>
		</view>
	</view>
</template>

<script>
	
	import instance from '../../common/instance.js';
	
	export default {
		
		data() {
			return {
				maitainList:[],
				totleMateriaFee:"",
				listData:["123", "413"],
				labels:["五菱", "小型客车", "定点维修"],
				materialFeeListTitle:["配件或材料名称", "型号", "配件类型", "供货商", "联系电话", "数量", "单位", "进货单价(￥)", "加价比例", "合计(￥)", "状态"],
				workCostListTitles : ["地点", "免费公里数", "公里数", "公里单价", "单价", "状态", "总价", "施救金额总计"],
				materialFeeList:[],
				materialNames:["机油格", "滤清器", "机油", "雨刮器", "阀门", "空调滤清器"],
				eid : 219,
				detailData:{},
				repairTime:"",
				repairList:[],
				logs:"",
				materialcostList:[],
				workcostList:[],
				oldAttachs:[],
				afterAttachs:[],
				beforAttachs:[],
				repairTypeName:'',
				curStatus:{},
			}
		},
		onLoad:function(option) {
			this.eid = option.eid;
			console.log("this.eid: " + this.eid);
			console.log("option: " + JSON.stringify(option));
			this.httpGetDetail();
		},
		methods: {
			httpGetDetail() {
				console.log("getDetail");
				var data = {
					flag: 'audit',
					eid: this.eid,
				};
				console.log("this.eid: " + this.eid);
				var token = null;
				document.addEventListener('plusready',function () {
				    // 在这里调用plus api
					var info = plus.runtime.arguments;
					var cmd = JSON.parse(info);
					token = cmd.value;
					console.log("token = " + token);
				},false);
				
				var serverUrl = instance.domain + 'designated/fixrepair/appDetail.do';
				console.log("serverUrl: " + serverUrl);
				if (this.last_id) {
					//说明已有数据，目前处于上拉加载
					this.status = 'loading';
					console.log(data);
				}
				if (token == null){
					token = 'd_muarggMsmg0TC3ARQqfUFUpQ7Bi7PMjfSf070G_TECzA===@4BC445EB19EF498DA826D6CCF81282B8';
				}
				uni.request({
					url: serverUrl,
					header: {
						ACCESS_TOKEN: token,
					},
					data: data,
					method: "GET",
					success: data => {
						console.log("data.data = " + JSON.stringify(data.data));
						console.log("data.statusCode = " + data.statusCode)
						if (data.statusCode != 200) {
							uni.showToast({
								title:"网络请求失败",
								icon:"none"
							});
							return;
						}
						let value = data.data.data;
						console.log("value: " + JSON.stringify(value));
						this.detailData = value;
						this.logs = value.repairLogs;
						console.log("this.logs: " + JSON.stringify(this.logs));
						let repair = value.repairList;
						this.repairList = repair;
						this.setMaitainList(repair);
						this.setMaterialFeeList(value);
						this.setWorkCostList(value);
						// this.oldAttachs = value.oldAttachs;
						// this.beforAttachs = value.beforAttachs;
						// this.afterAttachs = value.afterAttachs;
						// this.workcostList = value.workcostList;
						// this.materialcostList = value.materialcostList;
						this.repairTypeName = value.repairTypeName;
						this.curStatus = this.logs[0];
						console.log("this.curStatus: " + JSON.stringify(this.curStatus));
						// console.log("this.repairTypeName: " + this.repairTypeName);
					},
					fail: (data, code) => {
						console.log('fail:' + JSON.stringify(data));
					}
				});
			},
			setMaitainList(repair){
				this.maitainList = [{"title":"送修司机：", "value": repair.driverName},
									{"title":"车牌号码：", "value": repair.carNo},
									{"title":"型号：", "value": repair.modelName},
									{"title":"送修里程数：", "value": repair.driveKm},
									{"title":"报牌日期：",  "value": repair.carReportTime},
									{"title":"报修项目：", "value": repair.repairProNames},
									{"title":"故障描述：", "value": repair.reason}];
				console.log("this.maitainList: " + JSON.stringify(this.maitainList));
			},
			setMaterialFeeList(value){
				// this.materialFeeList = value.materialcostList;
				let list = value.materialcostList;
				if (list.length == 0) {
					console.log("Error:materialcostList is empty!");
					return;
				}
				console.log("list: " + JSON.stringify(list));
				let keys = ["materialName",
								"msize", 
								"grade", 
								"supplierName",
								"supplierPhone",
								"account",
								"unit",
								"afTaxiPrice",
								"ratio",
								"totalPrice",
								"state"];
				var array = [];
				let totalPrice = 0;
				for (var i = 0; i < keys.length; i++) {
					let ar = [];
					for (var j = 0; j < list.length; j++) {
						let obj = list[j];
						let key = keys[i];
						let value = obj[key];
						console.log("obj: " + obj);
						console.log("value: " + value);
						ar.push(value);
						if (key == "totalPrice") {
							totalPrice = totalPrice + parseFloat(value);
						}
					}
					array.push(ar);
				}
				this.materialFeeList = array;
				this.totleMateriaFee = totalPrice.toString();
				console.log("this.materialFeeList: " + JSON.stringify(this.materialFeeList));
			},
			setWorkCostList(value){
				let workList = value.workcostList;
				
			}
		}
	}
</script>

<style>
	/* #ifndef APP-NVUE */
	page {
		display: flex;
		flex-direction: column;
		box-sizing: border-box;
		background-color: #efeff4;
		min-height: 100%;
		height: auto;
	}
	/* #endif */
	view {
		font-size: 35rpx;
		line-height: inherit;
	}
	.section{
		background-image: url(../../static/cell_back.png);
		background-repeat: no-repeat;
		height: flex;
		width: 100%;
		padding-bottom: 40rpx;
		background-color: #00000000;
		background-size: 100% 100%;
	}
	.margin_left{
		margin-left: 20rpx;
	}
	.margin_left_section{
		margin-left: 40rpx;
	}
	.table_totle_justify{
		margin-left: 0rpx;
		margin-right: 0rpx;
		margin-bottom: 0rpx;
		padding-top: 0rpx;
		display: flex;
		flex-direction: row;
		justify-content: space-between;
	}
	
	.alignment_justify{
		margin-left: 40rpx;
		margin-right: 40rpx;
		margin-bottom: 20rpx;
		padding-top: 30rpx;
		display: flex;
		flex-direction: row;
		justify-content: space-between;
	}
	.alignment_left{
		display: flex;
		flex-direction: row;
		align-items: center;
	}
	.title_left{
		padding-top: 40rpx;
		padding-bottom: 20rpx;
		margin-left: 40rpx;
	}
	.schedule_icon{
		background-image: url(../../static/icon_schedule.png);
		background-size: contain;
		background-repeat: no-repeat;
		margin-left: 60rpx;
		margin-right: 10rpx;
		width: 32rpx;
		height: 32rpx;
	}
	.schedule_time{
		margin-left: 10rpx;
		color: #808080;
	}
	.overflowSuspension{
		width: flex;
		white-space: nowrap;
		text-overflow: 
		ellipsis;overflow: hidden;
	}
	.schedule_line{
		width: 1rpx;
		height: 80rpx;
		margin-left: 75rpx;
		margin-right: 30rpx;
		background-color: #e3e3e3;
	}
	.lable{
		background-color: #f4643e;
		color: #ffffff;
		padding-left: 16rpx;
		padding-right: 16rpx;
		padding-bottom: 4rpx;
		padding-top: 2rpx;
		margin-right: 0rpx;
		margin-left: 50rpx;
		margin-bottom: 10rpx;
		border-radius: 6rpx;
		white-space: nowrap;
	}
	.backColorYellow{
		background-color: #cda300;
	}
	.line_table_horizatal{
		background-color: #cccccc;
		height: 1px;
		margin-left: 10rpx;
		margin-right: 10rpx;
		margin-top: 0rpx;
		margin-bottom: 10rpx;
		-webkit-transform: scaleY(.5);
		transform: scaleY(.5);
		content: "";
	}
	.line_seperate{
		background-color: #cccccc;
		height: 1px;
		margin-left: 20rpx;
		margin-right: 20rpx;
		margin-top: 10rpx;
		margin-bottom: 10rpx;
		-webkit-transform: scaleY(.5);
		transform: scaleY(.5);
		content: "";
	}
	.corner_gray_back{
		border-radius: 10rpx;
		padding-top: 20rpx;
		padding-bottom: 20rpx;
		background-color: #f5f5f5;
		height: flex;
		margin-left: 40rpx;
		margin-right: 40rpx;
		margin-top: 20rpx;
	}
	.row_title{
		margin-bottom: 15rpx;
		
	}
	.table_border{
		border: 1rpx solid #eeeeee;
		border-radius: 0;
	}
	.table_totle_value_border{
		height: 60rpx;
		line-height: 60rpx;
		/* margin-left: 0rpx;
		margin-right: 0rpx;
		border: 1rpx solid #eeeeee;
		border-radius: 0; */
	}
	
	.table_margin{
		margin-left: 28rpx;
		margin-right: 28rpx;
	}
	.remark{
		margin-left: 40rpx;
		margin-right: 40rpx;
		margin-top: 20rpx;
		margin-bottom: 20rpx;
		color: #707070;
	}
	.scroll-view_H {
		white-space: nowrap;
	}
	.scroll-view-item_H {
		display: inline-block;
		width: 260rpx;
		height: 60rpx;
		margin-top: -2rpx;
		margin-bottom: -2rpx;
		margin-left: -2rpx;
		line-height: 60rpx;
		text-align: center;
	}
	.table_value_item{
		display: inline-block;
		-webkit-flex: 1;
		flex: 1;
		height: 60rpx;
		margin-top: -2rpx;
		margin-bottom: -2rpx;
		margin-left: -2rpx;
		line-height: 60rpx;
		text-align: center;
	}
	.title_table{
		display: inline-block;
		background-color: #eeeeee;
		width: 260rpx;
		text-align: end;
		padding-top: 6rpx;
		padding-bottom: 6rpx;
		padding-right: 10rpx;
	}
	.line_table_bottom{
		background-color: #eeeeee;
		height: 1rpx;
		margin-top: 0rpx;
		margin-bottom: 0rpx;
		content: "";
	}
	.line_table_right{
		background-color: #eeeeee;
		width: 1rpx;
		margin-top: 0rpx;
		margin-bottom: 0rpx;
		content: "";
	}
	.line_table_seperate_ampty{
		background-color: #cccccc00;
		height: 1rpx;
		margin-right: 25rpx;
		margin-top: 0rpx;
		margin-bottom: 0rpx;
		-webkit-transform: scaleY(.5);
		transform: scaleY(.5);
		content: "";
	}
	.line_table_seperate_first{
		background-color: #cccccc;
		height: 1rpx;
		margin-right: 25rpx;
		margin-left: 25rpx;
		margin-top: 0rpx;
		margin-bottom: 0rpx;
		-webkit-transform: scaleY(.5);
		transform: scaleY(.5);
		content: "";
	}
	.line_table_seperate_last{
		background-color: #cccccc;
		height: 1rpx;
		margin-right: 10rpx;
		margin-left: 25rpx;
		margin-bottom: 0rpx;
		-webkit-transform: scaleY(.5);
		transform: scale Y(.5);
		content: "";
	}
	.lineTable_vertical{
		background-color: #cccccc;
		height: flex;
		margin-left: 0rpx;
		margin-right: 0rpx;
		margin-top: 0rpx;
		margin-bottom: 0rpx;
		-webkit-transform: scaleY(.5);
		transform: scaleY(.5);
		content: "";
	}
</style>
