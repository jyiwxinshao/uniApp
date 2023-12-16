<template>
	<view class="container">
		<uni-card :title="user?user.userName:' '"
			:thumbnail="(user&&user.avatar)?BaseURL+user.avatar:'/static/kun1.png'" class="t1">
			<uni-list>
				<template v-if="isLogin">
					<uni-list-item title="个人信息" showArrow="" clickable="" @click="toUserinfo"></uni-list-item>
					<uni-list-item title="订单列表" showArrow=""></uni-list-item>
					<uni-list-item title="修改密码" showArrow=""></uni-list-item>
					<uni-list-item title="意见反馈" showArrow="" to="/pages/feedback/feedback"></uni-list-item>
					<uni-list-item title="退出登录" clickable="" @click="logout" showArrow=""></uni-list-item>
				</template>
				<uni-list-item to="/pages/login/login" v-else title="用户登录" showArrow=""></uni-list-item>
				<uni-list-item title="用户注册" to="/pages/reg/reg" showArrow=""></uni-list-item>
			</uni-list>
		</uni-card>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				isLogin: false,
				user: null
			}
		},
		methods: {
			logout() {
				uni.showModal({
					title: '提示',
					content: '是否确认退出登录？',
					showCancel: true,
					cancelText: '取消',
					confirmText: '确认',
					success: res => {
						if (res.confirm) {
							uni.removeStorageSync("mytoken");
							this.isLogin = false;
							this.user = null;
						}
					},
					fail: () => {},
					complete: () => {}
				});
			},
			toUserinfo() {
				uni.setStorageSync("userinfo", this.user);
				uni.navigateTo({
					url: '/pages/userInfo',
					success: res => {},
					fail: () => {},
					complete: () => {}
				});
			}
		},
		onShow() {
			// 判断有没有token,有的话去获取用户信息。
			let token = uni.getStorageSync("mytoken");
			if (token) {
				uni.request({
					url: this.BaseURL + '/prod-api/api/common/user/getInfo',
					method: 'GET',
					data: {},
					header: {
						Authorization: token
					},
					success: res => {
						// console.log(res.data);
						if (res.data.code == 200) {
							this.user = res.data.user;
							this.isLogin = true;
						} else {
							this.user = null;
							this.isLogin = false;
							uni.removeStorageSync("mytoken");
						}
					},
					fail: () => {},
					complete: () => {}
				});
			} else {
				this.isLogin = false;
			}
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