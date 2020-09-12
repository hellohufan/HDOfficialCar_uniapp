<template>
	<view class="topBackView">
		<view style="background-color: #00000000;">
			<uni-search-bar radius="100" placeholder="请输入您要查找的司机名字" clearButton="auto" cancelButton="auto" @input="input" @cancel="cancel"  @confirm="search" />
		</view>
		<view class="uni-list">
			<block v-for="(value, index) in listData" :key="index">
				<view class="uni-list-cell" @click="goDetail(value.id)"> 
					<view class="contentView">
						<view class="carNo">
							<text>车牌号：{{value.carNo}}</text>
							<view class="requestStatus">{{value.levelName}}</view>
						</view>
						<view class="line"></view>
						<view class="cellRowItem">
							<image class="cellRowItemIcon" src="../../static/repair_factory.png"></image>
							<text class = "overflowSuspension">维修厂: {{value.enquiryMaintenNames}}</text>
						</view>
						<view class="cellRowItem">
							<image class="cellRowItemIcon" src="../../static/repair_type.png"></image>
							<text>维修类型: {{value.repairTypeName}}</text>
						</view>
						<view class="cellRowItem">
							<image class="cellRowItemIcon" src="../../static/user_car_name.png"></image>
							<text class="darkGray overflowSuspension">司机: {{value.driverName}}</text>
							<text class="phone" @click.stop="telephone({phone:value.concatPhone})">{{value.concatPhone}}</text>
						</view>
						<view class="cellRowItem">
							<image class="cellRowItemIcon" src="../../static/time.png"></image>
							<text class="darkGray">送修时间: {{value.repairTime}}</text>
						</view>
					</view>
				</view>
			</block>
		</view>
		<uni-load-more :status="status" :icon-size="16" :content-text="contentText" />
	</view>
</template>

<script>
	import uniLoadMore from '@/components/uni-load-more/uni-load-more.vue';
	var dateUtils = require('../../common/util.js').dateUtils;
	import instance from '../../common/instance.js';
	export default {
		components: {
			uniLoadMore
		},
		data() {
			return {
				listData: [],
				last_id: '',
				reload: false,
				status: 'more',
				page: 1,
				adpid: '',
				contentText: {
					contentdown: '上拉加载更多',
					contentrefresh: '加载中',
					contentnomore: '没有更多'
				}
			};
		},
		onLoad() {
			console.log("[Index]:onLoad");
			this.adpid = this.$adpid;
			this.getList();
			
		},
		onPullDownRefresh() {
			this.reload = true;
			this.last_id = '';
			this.getBanner();
			this.getList();
		},
		onReachBottom() {
			this.status = 'more';
			this.getList();
		},
		methods: {
			search(res) {
				uni.showToast({
					title: '搜索：' + res.value,
					icon: 'none'
				})
			},
			input(res) {
				this.searchVal = res.value
			},
			cancel(res) {
				// uni.showToast({
				// 	title: '点击取消，输入值为：' + res.value,
				// 	icon: 'none'
				// })
			},
			
			getList() {
				console.log("getList");
				var data = {
					flag: 'doAuditing',
					page: this.page,
					rows: '5'
				};
				var token = null;
				document.addEventListener('plusready',function () {
				    // 在这里调用plus api
					var info = plus.runtime.arguments;
					var cmd = JSON.parse(info);
					token = cmd.value;
					console.log("token = " + token);
				},false);
				
				var serverUrl = instance.domain + 'mywork/repairapprove/queryOrderPage.do';
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
						uni.stopPullDownRefresh();
						console.log("data.data = " + data.data);
						console.log("data.statusCode = " + data.statusCode)
						if (data.statusCode != 200) {
							uni.showToast({
								title:"网络请求失败",
								icon:"none"
							});
							return;
						}
						console.log("1111111");
						var list = data.data.rows;
						if (list.length == 0){
							this.status = 'contentnomore'
							return;
						}
						this.listData = this.reload? ist: this.listData.concat(list);
						this.last_id = list[list.length - 1].id;
						this.page++;
						console.log("22222");
						console.log("list: " + JSON.stringify(list));
					},
					fail: (data, code) => {
						console.log('fail:' + JSON.stringify(data));
					}
				});
			},
			goDetail: function(eid) {
				// let detail = {
				// 	author_name: e.author_name,
				// 	cover: e.cover,
				// 	id: e.id,
				// 	post_id: e.post_id,
				// 	published_at: e.published_at,
				// 	title: e.title
				// };
				// console.log(detail)
				uni.navigateTo({
					url: '../maintainCar/maintainCarDetail?eid=' + eid
				});
			},
			telephone: function(phone){
				var info = plus.runtime.arguments;
				var cmd = JSON.parse(info);
				console.log("info = " + info);
				console.log("cmd = " + cmd);
				console.log("phone = " + phone.phone);
				console.log("token = " + cmd.value);
				// var arguments = plus.runtime.arguments;
				uni.showToast({
					title:"argument",
					icon:"none",
				})
			}
		},
		onBackPress() {
			// #ifdef APP-PLUS
			plus.key.hideSoftKeybord();
			// #endif
		}
	}
</script>

<style>
	/* 头条小程序组件内不能引入字体 */
	/* #ifdef MP-TOUTIAO */
	@font-face {
		font-family: uniicons;
		font-weight: normal;
		font-style: normal;
		src: url('~@/static/uni.ttf') format('truetype');
	}
	
	/* #endif */
	
	/* #ifndef APP-NVUE */
	page {
		display: flex;
		flex-direction: column;
		box-sizing: border-box;
		background-color: #efeff4;
		min-height: 100%;
		height: auto;
	}
	
	view {
		font-size: 30rpx;
		line-height: inherit;
	}
	.darkGray{
		color: #707070;
	}
	.contentView{
		width: 100%;
		background-color: #00000000;
		background-image: url(../../static/cell_back.png);
		height: 440rpx;
		background-size: 100% 100%;
		background-repeat: no-repeat;
	}
	.carNo {
		margin-left: 40rpx;
		padding-top: 40rpx;
		display: flex;
		flex-direction: row;
		justify-content: space-between;
	}
	.line{
		margin-left: 40rpx;
		margin-right: 40rpx;
		margin-top: 20rpx;
		top: 20rpx;
		height: 1rpx;
		-webkit-transform: scaleY(.5);
		transform: scaleY(.5);
		background-color: #c8c7cc;
	}
	.requestStatus{
		width: flex;
		height: 50rpx;
		padding-left: 10rpx;
		padding-right: 10rpx;
		text-align: center;
		line-height: 50rpx;
		font-size: 30rpx;
		border-radius: 8rpx;
		align-content: center;
		margin-right: 40rpx;
		color: #ffffff;
		background-color: #f4643e;
	}
	.cellRowItem{
		margin-left: 40rpx;
		margin-right: 40rpx;
		padding-top: 20rpx;
		display: flex;
		height: 50rpx;
		flex-direction: row;
		line-height: 50rpx;
		justify-content: flex-start;
	}
	.searchView {
		/* display: block; */
		padding: 30rpx;
		color: #3b4144;
		background-color: #ffffff00;
		height: auto;
		/* line-height: 2rpx; */
	}
	.phone{
		margin-left: 60rpx;
		color: #707070;
	}
	.phone:hover {
		color: #f4643e;
	}
	.cellRowItemIcon{
		margin-left: 40;
		margin-top: 8rpx;
		margin-right: 10rpx;
		width: 32rpx;
		height: 32rpx;
	}
	/* #endif */
	
	.uni-media-list-body {
		height: auto;
		justify-content: space-around;
	}
	
	.uni-media-list-text-top {
		height: 248rrpx;
		font-size: 56rrpx;
		overflow: hidden;
	}
	.overflowSuspension{
		white-space: nowrap;
		text-overflow: 
		ellipsis;overflow: hidden;
	}
	.uni-media-list-text-bottom {
		display: flex;
		flex-direction: row;
		justify-content: space-between;
	}
</style>
