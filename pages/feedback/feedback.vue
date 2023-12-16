<template>
	<view class="container">
		<p class="top">反馈标题</p>
		<uni-easyinput v-model="title" placeholder="请概述问题" type="text" maxlength="-1" class="top" />
		<p class="top">反馈内容</p>
		<uni-easyinput v-model="content" placeholder="请填写问题描述以便我们提供更好的帮助" type="textarea" autoHeight maxlength="-1"
			class="top" />
		<button class="top" type="primary" @click="send">提交</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: '',
				content: ''
			}
		},
		methods: {
			send() {
				const title = this.title;
				const content = this.content;
				if (title == "" || content == "") {
					uni.showToast({
						title: " 请输入内容！",
						icon: "none"
					})
				} else {
					uni.request({
						url: this.BaseURL + '/prod-api/api/common/feedback',
						method: 'POST',
						data: {
							content: title,
							title: title
						},
						header: {
							Authorization: uni.getStorageSync("mytoken")
						},
						success: res => {
							if (res.data.code == 200) {
								uni.showToast({
									title: "反馈成功！",
									icon: "none"
								});
								this.title = "";
								this.content = "";
							}

						},
						fail: () => {},
						complete: () => {}
					});
				}
			}
		},
		onLoad() {
			uni.setNavigationBarTitle({
				title: "意见反馈"
			})
		}
	}
</script>

<style>
	.container {
		padding: 20px;
		font-size: 14px;
		line-height: 24px;

		.top {
			margin-top: 10px;
		}
	}
</style>