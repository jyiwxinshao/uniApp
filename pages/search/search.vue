<template>
	<view class="container">
		<uni-easyinput v-model="value" placeholder="想看什么,搜索一下" type="text" confirmType="search" suffixIcon="search"
			@iconClick="search" class="search" @keyup.enter="search" />

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
				newsClass: [],
				newsClass1: [],
				newsList: [],
				value: '',
				msg: ''
			}
		},
		methods: {
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
							this.newsList = res.data.rows.filter(item => item.title.includes(this.msg));
						}
					},
					fail: () => {},
					complete: () => {
						uni.hideLoading();
					}
				});
			},
			changeList(e) {
				this.getNewsList(this.newsClass[e.currentIndex].id);
			},
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
			toContent(id) {
				uni.navigateTo({
					url: '/pages/content/content?id=' + id,
					success: res => {},
					fail: () => {},
					complete: () => {}
				});
			}
		},

		onLoad(e) {
			this.msg = e.inputValue;

			uni.setNavigationBarTitle({
				title: this.msg + "_搜索结果"
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
		},
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

		.search {
			padding-bottom: 10px;
		}
	}
</style>