<template>
	<view class="container">
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" v-if="isShow"
			style="width: 100%;height: 510rpx;">
			<swiper-item v-for="(item,index) in newsList">
				<image :src="BaseURL+item.imgUrl" mode="scaleToFill" style="width: 100%;height: 510rpx;"></image>
			</swiper-item>
		</swiper>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				data: {},
				isShow: false,
				newsList: []
			}
		},
		methods: {

		},
		onLoad(e) {
			uni.request({
				url: this.BaseURL + '/prod-api/api/logistics-inquiry/logistics_company/' + e.id,
				method: 'GET',
				data: {},
				header: {
					Authorization: uni.getStorageSync("mytoken")
				},
				success: res => {
					if (res.data.code == 200) {
						this.data = res.data.data;
						uni.setNavigationBarTitle({
							title: this.data.name
						});
						if (res.data.data.newsList !== 0) {
							this.newsList = res.data.data.newsList;
							this.isShow = true;
						}
					}
				},
				fail: () => {},
				complete: () => {}
			});
		}
	}
</script>

<style>
	.container {
		padding: 20px;
		font-size: 14px;
		line-height: 24px;
	}
</style>