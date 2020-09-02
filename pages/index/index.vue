<template>
	<view>
		<view class="searchView">
			<uni-search-bar radius="100" placeholder="请输入您要查找的司机名字" clearButton="auto" cancelButton="auto" @input="input" @cancel="cancel"  @confirm="search" />
		</view>
		<view class="uni-list">
			<block v-for="(value, index) in listData" :key="index">
				<view class="uni-list-cell" hover-class="uni-list-cell-hover" @click="goDetail(value)">
					<view class="uni-media-list">
						<image class="uni-media-list-logo" :src="value.cover"></image>
						<view class="uni-media-list-body">
							<view class="uni-media-list-text-top">{{ value.title }}</view>
							<view class="uni-media-list-text-bottom">
								<text>{{ value.author_name }}</text>
								<text>{{ value.published_at }}</text>
							</view>
						</view>
					</view>
				</view>
			</block>
		</view>
	</view>
</template>

<script>
	import uniLoadMore from '@/components/uni-load-more/uni-load-more.vue';
	var dateUtils = require('../../common/util.js').dateUtils;
	
	export default {
		components: {
			uniLoadMore
		},
		data() {
			return {
				banner: {},
				listData: [],
				last_id: '',
				reload: false,
				status: 'more',
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
				uni.showToast({
					title: '点击取消，输入值为：' + res.value,
					icon: 'none'
				})
			},
			
			getList() {
				var data = {
					column: 'id,post_id,title,author_name,cover,published_at' //需要的字段名
				};
				if (this.last_id) {
					//说明已有数据，目前处于上拉加载
					this.status = 'loading';
					data.minId = this.last_id;
					data.time = new Date().getTime() + '';
					data.pageSize = 10;
				}
				uni.request({
					url: 'https://unidemo.dcloud.net.cn/api/news',
					data: data,
					success: data => {
						if (data.statusCode == 200) {
							let list = this.setTime(data.data);
							this.listData = this.reload ? list : this.listData.concat(list);
							this.last_id = list[list.length - 1].id;
							this.reload = false;
							console.log(this.listData)
						}
					},
					fail: (data, code) => {
						console.log('fail' + JSON.stringify(data));
					}
				});
			},
			setTime: function(items) {
				var newItems = [];
				items.forEach(e => {
					newItems.push({
						author_name: e.author_name,
						cover: e.cover,
						id: e.id,
						post_id: e.post_id,
						published_at: dateUtils.format(e.published_at),
						title: e.title
					});
				});
				return newItems;
			},
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
		font-size: 14px;
		line-height: inherit;
	}
	.infoBack {
		height: 500rpx;
		background-image: url(../../static/tri_detail_bg.png);
		background-repeat: no-repeat;
		background-size: 100% 100%;
	}
	
	.searchView {
		/* display: block; */
		padding: 15px;
		color: #3b4144;
		background-color: #ffffff00;
		font-size: 14px;
		height: auto;
		/* line-height: 2px; */
	}
	/* #endif */
	
	.uni-media-list-logo {
		width: 180rpx;
		height: 140rpx;
	}
	
	.uni-media-list-body {
		height: auto;
		justify-content: space-around;
	}
	
	.uni-media-list-text-top {
		height: 74rpx;
		font-size: 28rpx;
		overflow: hidden;
	}
	
	.uni-media-list-text-bottom {
		display: flex;
		flex-direction: row;
		justify-content: space-between;
	}
</style>
