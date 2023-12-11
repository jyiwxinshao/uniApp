<template>
	<view class="container">
		<uni-collapse>
			<uni-collapse-item v-for="(item,index) in serviceType" :title="item">
				<uni-grid :column="5">
					<uni-grid-item v-for="(item1,index1) in getService(item)">
						<view class="box">
							<image :src="'http://124.93.196.45:10001'+item1.imgUrl" mode="aspectFit"></image>
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

		}
	}
</script>

<style lang="scss">
	.container {
		padding: 20px;
		font-size: 14px;
		line-height: 24px;
	}
</style>