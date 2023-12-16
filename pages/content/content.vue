<template>
	<view v-if="isShow" class="container">
		<text>{{item.publishDate}}</text>
		<view class="t">
			<text>评论数:{{item.commentNum}}</text>
			<text>点赞数:{{item.likeNum}}</text>
			<text>阅读数:{{item.readNum}}</text>
		</view>
		<view class="line"></view>
		<image :src="BaseURL+item.cover" mode="aspectFit"></image>
		<rich-text :nodes="item.content" class="content"></rich-text>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				item: {},
				isShow: false
			}
		},
		methods: {

		},
		onLoad(e) {
			uni.request({
				url: this.BaseURL + '/prod-api/press/press/' + e.id,
				method: 'GET',
				data: {},
				success: res => {
					if (res.data.code == 200) {
						this.item = res.data.data;
						this.isShow = true;
						uni.setNavigationBarTitle({
							title: this.item.title
						});
						this.item.content = this.item.content.replace(/\<img src="/gi,
							"<img src=\"http://124.93.196.45:10001");
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
		display: flex;
		flex-direction: column;
		align-items: center;
		padding: 20px;
		font-size: 14px;
		line-height: 24px;

		.content {
			width: 95%;

			img {
				width: 100%;
			}
		}

		.t {
			display: flex;
			flex-direction: row;
			justify-content: space-between;
			width: 95%;
		}

		.line {
			width: 100%;
			height: 2px;
			background-color: antiquewhite;
		}
	}
</style>