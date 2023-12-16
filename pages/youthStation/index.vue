<template>
	<view class="container">
		<uni-grid :column="3">
			<uni-grid-item v-for="(item,index) in regionData">
				<view class="box1" @click="toPolicy(item.id)">
					<image :src="BaseURL+item.imgUrl" mode="aspectFit"></image>
					<text>{{item.name}}</text>
				</view>
			</uni-grid-item>
		</uni-grid>
		<uni-card v-for="(item,index) in stationData">
			<view class="box2" @click="toDetails(item.id)">
				<view class="box4">
					<image :src="BaseURL+item.coverImgUrl" mode="aspectFill"></image>
				</view>
				<view class="box3">
					<text>{{item.name}}</text>
					<text>剩余床位:男床({{item.bedsCountBoy}})====女床({{item.bedsCountGirl}})</text>
					<text>地址:{{item.address}}</text>
				</view>
			</view>
			<uni-collapse>
				<uni-collapse-item title="站点介绍">
					<text>{{item.introduce}}</text>
				</uni-collapse-item>
			</uni-collapse>
		</uni-card>
		<uni-load-more :status="status" @clickLoadMore="loadmore"></uni-load-more>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				regionData: [],
				stationData: [],
				status: "more",
				curPage: 1
			}
		},
		methods: {
			toPolicy(id) {
				uni.navigateTo({
					url: '/pages/youth/policy/policy?id=' + id,
					success: res => {},
					fail: () => {},
					complete: () => {}
				});
			},
			toDetails(id) {
				uni.navigateTo({
					url: '/pages/youth/details/details?id=' + id,
					success: res => {},
					fail: () => {},
					complete: () => {}
				});
			},
			loadmore() {
				if (this.status == "more") {
					let token = uni.getStorageSync("mytoken");
					this.loadStation(this.curPage, 5, token);
				}
			},
			loadStation(curpage, num, token) {
				uni.request({
					url: this.BaseURL + '/prod-api/api/youth-inn/youth-inn/list',
					method: 'GET',
					data: {
						pageNum: curpage,
						pageSize: num
					},
					header: {
						Authorization: token
					},
					success: res => {
						if (res.data.code == 200) {
							res.data.rows.forEach(item => {
								this.stationData.push(item);
							});
							// this.stationData = res.data.rows;
							if (this.stationData.length < res.data.total) {
								this.status = "more";
								this.curPage = this.curPage + 1;
							} else {
								this.status = "no-more";
							}
						}
					},
					fail: () => {},
					complete: () => {}
				});
			}
		},
		onLoad(e) {
			uni.setNavigationBarTitle({
				title: "青年驿站"
			});
			// 获取人才政策区域
			uni.request({
				url: this.BaseURL + '/prod-api/api/youth-inn/talent-policy-area/list',
				method: 'GET',
				data: {},
				header: {
					Authorization: e.token
				},
				success: res => {
					if (res.data.code == 200) {
						this.regionData = res.data.data;
					}
				},
				fail: () => {},
				complete: () => {}
			});
			this.loadStation(this.curPage, 5, e.token);
		}
	}
</script>

<style lang="scss">
	.container {
		padding: 20px;
		font-size: 14px;
		line-height: 24px;

		.box1 {
			height: 100%;
			display: flex;
			flex-direction: column;
			align-items: center;
			justify-content: center;

			image {
				width: 150rpx;
				height: 150rpx;
			}
		}

		.box2 {
			display: flex;
			flex-direction: row;
			align-items: center;

			image {
				width: 150rpx;
				height: 150rpx;
			}
		}

		.box3 {
			margin-left: 10rpx;
			display: flex;
			flex-direction: column;
		}
	}
</style>