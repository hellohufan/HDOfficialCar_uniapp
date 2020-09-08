<template>
	<view class="topBackView">
		<view class="section">
			<view class="alignment_justify">
				<view>
					<text>当前节点：</text>
					<text style="color: #f4643e;">未审批</text>
				</view>
				<text style="color: #707070;">维修单号：47890</text>
			</view>
			<view class="uni-list">
				<block v-for="data in listData" :key="index">
					<view class="uni-list-cell" @click="goDetail(value)"> 
						<view class="alignment_left">
							<image class="schedule_icon"></image>
							<view class="schedule_time">2020-10-22 18:35</view>
						</view>
						<view class="alignment_left">
							<view class="schedule_line"></view>
							<text>未审批</text>
							<view class="alignment_left">
								<view></view>
							</view>
						</view>
					</view>
				</block>
			</view>
		</view>
		<view class="section">
			<view class="title_left">维修单详情</view>
			<view class="alignment_left">
				<block v-for="label in labels">
					<view class="lable">{{label}}</view>
				</block>
			</view>
			<view class="line_seperate"></view>
			<view class="alignment_left">
				<text class="alignment_left margin_left_section">送修时间:</text>
				<view class="schedule_time">2020-12-23 10:39</view>
			</view>
			<view class="corner_gray_back">
				<view class="alignment_left margin_left">
					<text class="">维修厂：</text>
					<view class="">福州市三牛汽车有限公司</view>
				</view>
				<view class="line_seperate"></view>
				<block v-for="data in maitainDetailTitles">
					<view class="alignment_left">
						<view class="row_title margin_left">{{data}}</view>
						<view class="row_title">吴三桂</view>
					</view>
				</block>
			</view>
			<view class="remark">备注信息：开卷考试拉法基到拉萨 就开始大量分解肯定萨拉 卡上了酒店费打法打。</view>
		</view>
		<view class="section">
			<view class="title_left">材料费详情</view>
			<view class="line_table_seperate"></view>
			<view class="table_margin">
				<scroll-view class="scroll-view_H table_margin" scroll-x="true" @scroll="scroll">
					<block v-for="title in materialFeeDetailsTitle">
						<view class="title_table">{{title}}</view>
						<view id="demo1" class="scroll-view-item_H uni-bg-red">A</view>
						<view id="demo2" class="scroll-view-item_H uni-bg-green">B</view>
						<view id="demo3" class="scroll-view-item_H uni-bg-blue">C</view>
						<view class="line_table_seperate"></view>
					</block>
				</scroll-view>
			</view>	
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: '哇哈哈'
			}
		},
		onLoad() {
			this.listData = ["123", "413"];
			this.labels = ["五菱", "小型客车", "定点维修"];
			this.maitainDetailTitles = ["送修司机：", "车牌号码：", "型号：", "送修里程数：", "报牌日期：", "报修项目：", "故障描述"];
			this.materialFeeDetailsTitle = ["配件或材料名称", "型号", "配件类型", "供货商", "联系电话", "数量", "单位", "进货单价(元)", "加价比例", "合计", "状态", "材料金额合计"];
			this.materialNames = ["机油格", "滤清器", "机油", "雨刮器", "阀门", "空调滤清器"];
		},
		methods: {
	
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
	.alignment_justify{
		margin-left: 40rpx;
		margin-right: 40rpx;
		margin-bottom: 20rpx;
		padding-top: 20rpx;
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
	.table_margin{
		margin-left: 12rpx;
		margin-right: 37rpx;
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
		line-height: 60rpx;
		text-align: center;
		font-size: 35rpx;
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
	.line_table_seperate{
		background-color: #cccccc;
		height: 1rpx;
		margin-right: 25rpx;
		margin-top: 0rpx;
		margin-bottom: 0rpx;
		-webkit-transform: scaleY(.5);
		transform: scaleY(.5);
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
