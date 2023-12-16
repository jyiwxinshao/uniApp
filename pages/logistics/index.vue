<template>
	<view class="container">
		<uni-easyinput v-model="value" placeholder="物流运单查询" type="text" confirmType="search" suffixIcon="search"
			@iconClick="search" class="search" />

		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000"
			style="width: 100%;height: 375rpx;">
			<swiper-item v-for="(item,index) in swiperData">
				<image :src="BaseURL+item.imgUrl" mode="scaleToFill" style="width: 100%;height: 375rpx;"></image>
			</swiper-item>
		</swiper>

		<uni-grid :column="4">
			<uni-grid-item v-for="(item,index) in companyData">
				<view class="box" @click="toCompany(item.id)">
					<image :src="BaseURL+item.imgUrl" mode="aspectFit"></image>
					<text>{{item.name}}</text>
				</view>
			</uni-grid-item>
		</uni-grid>

		<uni-list>
			<uni-list-item v-for="(item,index) in elseCompany" :title="item.name" :thumb="BaseURL+item.imgUrl"
				clickable="" @click="toCompany(item.id)"></uni-list-item>
		</uni-list>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				value: '',
				swiperData: [],
				companyData: [],
				elseCompany: []
			}
		},
		methods: {
			search() {
				const inputValue = this.value;
				if (inputValue == "") {
					uni.showToast({
						title: "请输入物流运单！",
						icon: "none"
					})
				} else {
					uni.navigateTo({
						url: '/pages/logistics/search?inputValue=' + inputValue,
						success: res => {},
						fail: () => {},
						complete: () => {}
					});
				}
			},

			// 前往公司详情页面
			toCompany(id) {
				uni.navigateTo({
					url: '/pages/logistics/company?id=' + id,
					success: res => {},
					fail: () => {},
					complete: () => {}
				});
			},
		},
		onLoad(e) {
			uni.setNavigationBarTitle({
				title: "物流查询"
			});

			//获取主页轮播数据
			uni.request({
				url: this.BaseURL + '/prod-api/api/logistics-inquiry/ad-banner/list',
				methods: "GET",
				data: {},
				header: {
					Authorization: e.token
				},
				success: res => {
					if (res.data.code == 200) {
						this.swiperData = res.data.data;
					}
				},
				fail: () => {},
				complete: () => {}
			});

			// 获取公司数据
			uni.request({
				url: this.BaseURL + '/prod-api/api/logistics-inquiry/logistics_company/list',
				method: "GET",
				data: {},
				header: {
					Authorization: e.token
				},
				success: res => {
					if (res.data.code == 200) {
						let allData = res.data.data;

						// 根据sort属性排序
						allData.sort((a, b) => {
							return b.sort - a.sort;
						});

						// 取前12个数据赋给companyData
						this.companyData = allData.slice(0, 12);

						// 剩余的数据根据name属性首字母降序排序赋给elseCompany
						this.elseCompany = allData.slice(12); // 取剩余的数据
						this.elseCompany.sort((a, b) => {
							return b.name.localeCompare(a.name); // 按首字母降序排列
						});
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

		.search {
			padding-bottom: 10px;
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
	}
</style>