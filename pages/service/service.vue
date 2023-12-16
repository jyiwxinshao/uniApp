<template>
	<view class="container">
		<uni-collapse>
			<uni-collapse-item v-for="(item,index) in serviceType" :title="item">
				<uni-grid :column="5">
					<uni-grid-item v-for="(item1,index1) in getService(item)">
						<view class="box">
							<image :src="BaseURL+item1.imgUrl" mode="aspectFit"></image>
							<text>{{item1.serviceName.length>4?item1.serviceName.substr(0,3)+"..":item1.serviceName}}</text>
						</view>
					</uni-grid-item>
				</uni-grid>
			</uni-collapse-item>
		</uni-collapse>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				serviceData: [],
				serviceType: []
			}
		},
		methods: {
			getService(type) {
				uni.showLoading({
					title: "",
					mask: false
				});

				if (type != "其他服务") {
					let service = this.serviceData.filter(item => {
						uni.hideLoading();
						return item.serviceType == type;
					});
					return service;
				} else {
					let service = this.serviceData.filter(item => {
						uni.hideLoading();
						return item.serviceType == null;
					});
					return service;
				}
			}
		},
		onLoad() {
			//访问网络获取所有服务
			let service = uni.getStorageSync("service");
			if (service) {
				this.serviceData = service;
				//把不同的服务数据类型保存到serviceType数组中
				this.serviceData.forEach((item) => {
					if (!this.serviceType.includes(item.serviceType) && item.serviceType != null) {
						this.serviceType.push(item.serviceType);
					}
				});
				this.serviceType.push("其他服务");
			}
		}
	}
</script>

<style lang="scss">
	.container {
		padding: 20px;
		font-size: 14px;
		line-height: 24px;

		.box {
			height: 100%;
			padding: 8rpx;
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
				overflow: hidden;
			}
		}
	}
</style>