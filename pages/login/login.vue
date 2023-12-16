<template>
	<view class="container">
		<uni-card title="用户登录">
			<uni-forms ref="form1" :modelValue="loginInfo" :rules="rules1">
				<uni-forms-item required="" label="用户名" name="name1">
					<uni-easyinput v-model="loginInfo.name1" placeholder="请输入用户名"></uni-easyinput>
				</uni-forms-item>
				<uni-forms-item required="" label="密码" name="pwd">
					<uni-easyinput v-model="loginInfo.pwd" placeholder="请输入密码" type="password"></uni-easyinput>
				</uni-forms-item>
			</uni-forms>
			<button type="primary" @click="login">登录</button>
		</uni-card>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				loginInfo: {
					name1: "",
					pwd: ""
				},
				rules1: {
					name1: {
						rules: [{
							required: true,
							errorMessage: "用户名不能为空！"
						}]
					},
					pwd: {
						rules: [{
							required: true,
							errorMessage: "密码不能为空！"
						}]
					}
				}
			}
		},
		methods: {
			login() {
				this.$refs.form1.validate().then(res => {
					uni.request({
						url: this.BaseURL + '/prod-api/api/login',
						method: 'POST',
						data: {
							username: this.loginInfo.name1,
							password: this.loginInfo.pwd
						},
						success: res => {
							if (res.data.code == 200) {
								uni.setStorageSync("mytoken", res.data.token);
								uni.navigateBack({
									delta: 1
								});
							} else {
								uni.showToast({
									title: res.data.msg,
									icon: 'none'
								});
							}
						},
						fail: () => {},
						complete: () => {}
					});
				}).catch(err => {
					console.log(err)
				})
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