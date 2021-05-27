<template>
	<view>
		<view class="mp-login">
			<view class="mp-logo">
				<image src="/static/logo.svg" mode="widthFix"></image>
			</view>
			<view class="mp-tips important">为提供优质服务，需要获取你的以下信息:</view>
			<view class="mp-tips">你的公开信息(头像、昵称等)</view>
			<view class="mp-login-btn">
				<u-button @click="doLogin()" type="primary">微信用户一键登录</u-button>
			</view>
		</view>
		<view class="agreement u-flex">
			<view  class="checkbox" >
				<u-checkbox v-model="agreementChecked" size="42" :disabled="false"></u-checkbox>			
			</view>
			<view class="agreement-text">
				我已认真阅读、理解并同意
				<text class="agreement-link" @click="agreement1">《服务条款》</text>
				<text class="agreement-link">《隐私条款》</text>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				agreementChecked: false,
				canIUseGetUserProfile: true
			};
		},
		methods:{
			//登录
			doLogin(){
				if(!this.agreementChecked){
					this.$u.toast('请先阅读并勾选服务协议');
					return;
				}
				this.$zoomla.getWxUserInfo();
			},
			agreement1(){
				this.$u.toast('协议')
			}
		},
		onLoad() {
			if(!uni.getUserProfile){
				this.canIUseGetUserProfile = false;
			}
		}
	}
</script>

<style lang="scss" scoped>
.mp-login{
	.mp-tips{
		&.important{font-weight: 600;}
		& + .mp-tips{padding-top: 10rpx;color: #606266;}
	}
}
.mp-login-btn{margin: 40rpx 0;}
.tips{font-size: 26rpx;color: #82848A;margin-top: 30rpx;}
.agreement{padding-left: 20rpx; align-items: flex-start;}
.agreement-text{margin-left: -8rpx;	color: $u-tips-color;}
.agreement-link{color: $u-type-warning-dark;}
</style>
