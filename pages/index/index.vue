<template>
	<view class="container">
		<uni-easyinput v-model="value" placeholder="想看什么,搜索一下" type="text" confirmType="search" suffixIcon="search"
			@iconClick="search" class="search" />

		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000"
			style="width: 100%;height: 250rpx;">
			<swiper-item v-for="(item,index) in swiperData">
				<image :src="BaseURL+item.advImg" mode="scaleToFill" style="width: 100%;height: 250rpx;"></image>
			</swiper-item>
		</swiper>

		<uni-grid :column="5">
			<uni-grid-item v-for="(item,index) in serviceData">
				<view class="box" v-if="index!=9" @click="toService1(item.serviceName)">
					<image :src="BaseURL+item.imgUrl" mode="aspectFit"></image>
					<text>{{item.serviceName}}</text>
				</view>
				<view class="box" v-else @click="toService">
					<text>全部服务</text>
				</view>
			</uni-grid-item>
		</uni-grid>

		<uni-segmented-control :values="newsClass1" style-type="text" @clickItem="changeList"></uni-segmented-control>
		<uni-card v-for="(item,index) in newsList" :title="item.title" :thumbnail="BaseURL+item.cover"
			:extra="'评论数:'+item.commentNum" :subTitle="'发布日期:'+item.publishDate" note="Tips"
			@click="toContent(item.id)">
			<rich-text :nodes="item.content" class="content"></rich-text>
		</uni-card>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				swiperData: [], // 轮播数据
				serviceData: [], // 服务数据
				newsClass: [], // 新闻分类数据
				newsClass1: [], // 分段器上显示的数据
				newsList: [], // 新闻列表数据
				value: '' // 输入框输入数据
			}
		},
		methods: {
			// 获取新闻列表数据
			getNewsList(id) {
				uni.showLoading({
					title: "",
					mask: false
				});

				uni.request({
					url: this.BaseURL + '/prod-api/press/press/list',
					method: 'GET',
					data: {
						type: id
					},
					success: res => {
						if (res.data.code == 200) {
							this.newsList = res.data.rows;
						}
					},
					fail: () => {},
					complete: () => {
						uni.hideLoading();
					}
				});
			},

			// 获取新闻列表
			changeList(e) {
				this.getNewsList(this.newsClass[e.currentIndex].id);
			},

			// 前往新闻详情页面
			toContent(id) {
				uni.navigateTo({
					url: '/pages/content/content?id=' + id,
					success: res => {},
					fail: () => {},
					complete: () => {}
				});
			},

			// 前往全部服务页面
			toService() {
				uni.switchTab({
					url: "/pages/service/service"
				})
			},

			// 搜索
			search() {
				const inputValue = this.value;
				if (inputValue == "") {
					uni.showToast({
						title: "请输入内容！",
						icon: "none"
					})
				} else {
					uni.navigateTo({
						url: '/pages/search/search?inputValue=' + inputValue,
						success: res => {},
						fail: () => {},
						complete: () => {}
					});
				}
			},

			// 前往具体服务页
			toService1(name) {
				let token = uni.getStorageSync("mytoken");
				if (token) {
					if (name == '青年驿站') {
						uni.navigateTo({
							url: '/pages/youthStation/index?token=' + token,
							success: res => {},
							fail: () => {},
							complete: () => {}
						});
					} else if (name == '物流查询') {
						uni.navigateTo({
							url: '/pages/logistics/index?token=' + token,
							success: res => {},
							fail: () => {},
							complete: () => {}
						})
					}
				} else {
					uni.showToast({
						title: '需要登陆才能访问！',
						icon: 'none'
					});
				}
			}
		},

		onLoad() {
			uni.setNavigationBarTitle({
				title: "智慧城市平台"
			});

			//获取主页轮播数据
			uni.request({
				url: this.BaseURL + '/prod-api/api/rotation/list',
				methods: "GET",
				data: {},
				success: res => {
					if (res.data.code == 200) {
						this.swiperData = res.data.rows;
					}
				},
				fail: () => {},
				complete: () => {}
			});

			uni.request({
				url: this.BaseURL + '/prod-api/api/service/list',
				method: "GET",
				data: {},
				success: res => {
					if (res.data.code == 200) {
						this.serviceData = res.data.rows;
						uni.setStorageSync("service", this.serviceData);
						this.serviceData.sort((a, b) => {
							return b.sort - a.sort;
						});
						this.serviceData.length = 10;
					}
				},
				fail: () => {},
				complete: () => {}
			});

			uni.request({
				url: this.BaseURL + '/prod-api/press/category/list',
				method: 'GET',
				data: {},
				success: res => {
					if (res.data.code == 200) {
						this.newsClass = res.data.data;
						this.newsClass.forEach((item) => {
							this.newsClass1.push(item.name);
						});
						//获取新闻列表数据
						this.getNewsList(this.newsClass[0].id);
					}
				},
				fail: () => {},
				complete: () => {}
			});
		}
	}
</script>

<style lang="scss">
	.container {
		padding: 20px;
		font-size: 14px;
		line-height: 24px;

		.content {
			display: -webkit-box;
			-webkit-box-orient: vertical;
			-webkit-line-clamp: 3;
			overflow: hidden;
		}

		.box {
			height: 100%;
			padding: 8rpx 0;
			display: flex;
			flex-direction: column;
			align-items: center;
			justify-content: center;

			image {
				width: 80rpx;
				height: 80rpx;
			}

			text {
				white-space: nowrap;
			}
		}

		.search {
			padding-bottom: 10px;
		}
	}
</style>