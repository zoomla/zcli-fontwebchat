<template>
	<view>
		<view class="sett_top">
			<view class="sett_top_t">订单配送至</view>
			<view class="sett_top_add">{{address.safeName}}<view @click="chooseAddress()">修改地址</view></view>
		</view>
		<view class="sett_con">
			<u-checkbox v-model="usePurse">使用余额支付</u-checkbox>
			<text class="sett_purse">当前余额：¥{{vuex_user.purse.toFixed(2)}}</text>
		</view>
		<view class="sett_con">
			<view class="sett_con_top">详情</view>
			<view class="sett_con_list" v-for="(item, index) in productList" :key="index">
				<view class="sett_con_list_title">{{item.proname}}</view>
				<view class="sett_con_list_quantity">x<view class="sett_con_list_quantity1">{{item.pronum}}</view></view>
				<view class="sett_con_list_price">￥<view class="sett_con_list_price1">{{item.AllMoney}}</view></view>
			</view>
			<u-select v-model="show" :list="discountList"></u-select>
			<view class="sett_con_envelope" @click="show = true;">选择优惠券 〉</view>
		</view>
		<view class="sett_payment">
			<view class="sett_payment_left">总价:
				<view class="sett_payment_price">
					￥<view class="sett_payment_price1">{{totalFee}}</view>
				</view>
			</view>
			<view class="sett_payment_right" @click="confirm()">去支付</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				openid: "",
				show: false,
				address: {
					safeName: "",
					json: ""
				},
				discountList: [
					{
						value: '1',
						label: '满50减10'
					},
					{
						value: '2',
						label: '满100减30'
					},
					{
						value: '3',
						label: '满10减3'
					},
				],
				ids: "",
				productList: [],
				totalFee: 0,
				usePurse: false
			};
		},
		onLoad(options) {
			const that = this;
			if (!that.$zoomla.isLogin()) {
				return;
			}
			let mu = this.$store.state.vuex_user;
			that.openid = mu.openid;

			that.ids = options.ids;
			that.$u.get("cart_list", {
				openid: that.openid
			}).then(res => {
				for (let i = 0; i < res.result.CartDT.length; i++) {
					let item = res.result.CartDT[i];
					if (that.ids.indexOf(item.id.toString()) < 0) {
						continue;
					}
					that.totalFee += parseInt(item.AllMoney * 100);
					that.productList.push(item);
				}
				that.totalFee /= 100;
			});

			let rece = wx.getStorageSync("rece");
			if (!rece || typeof rece == "undefined") {
				that.address.safeName = "（暂未选择收货地址）";
				return;
			}
			that.address.json = JSON.stringify(rece);
			that.address.safeName =
				rece.provinceName +
				rece.cityName +
				rece.countyName +
				rece.detailInfo;
		},
		methods: {
			chooseAddress() {
				const that = this;
				wx.chooseAddress({
					success: function(address) {
						wx.setStorageSync("rece", address);
						that.address.json = JSON.stringify(address);
						that.address.safeName =
							address.provinceName +
							address.cityName +
							address.countyName +
							address.detailInfo;
					},
					fail: function() {},
					complete: function() {}
				});
			},
			confirm() {
				const that = this;
				if (!that.address.json) {
					that.chooseAddress();
					return;
				}
				if (that.usePurse && that.totalFee > that.$store.state.vuex_user.purse) {
					uni.showModal({
						title: "对不起，您的余额不足，请充值或使用现金支付",
						showCancel: false
					});
					return;
				}
				uni.showLoading({
					mask: true
				});
				// 递交订单
				that.$u.post("payment_cart", {
					openid: that.openid,
					cartIds: that.ids,
					fareDT: `[{"storeId":"1","total":"0.00"}]`, // 运费设置
					rece: that.address.json,
					use_sicon: 0,
					use_purse: (that.usePurse ? that.totalFee : 0)
				}).then(paymentCartRes => {
					let orderData = JSON.parse(paymentCartRes.addon);
					let payData = paymentCartRes.result;
					if (that.usePurse) {
						this.$zoomla.getMemberInfo();
						uni.showToast({
							title: "支付成功",
							icon: "none"
						});
						// 支付成功，扣减购物车，完成订单
						that.$u.post("payment_success", {
							openid: that.openid,
							orderIds: orderData.orderIds,
							cartIds: orderData.cartIds
						}).then(paymentSuccessRes => {
							that.EventBus.$emit("cartHaveChange");
						});
						// 跳转到首页
						setTimeout(function() {
							uni.switchTab({
								url: "/pages/home/index"
							});
						}, 650);
						return;
					}
					// 唤起微信支付
					wx.requestPayment({
						"timeStamp": payData.timeStamp,
						"nonceStr": payData.nonceStr,
						"package": payData.package,
						"signType": payData.signType,
						"paySign": payData.paySign,
						complete: function() {
							uni.hideLoading();
						},
						success: function(payRes) {
							uni.showToast({
								title: "支付成功",
								icon: "none"
							});
							// 支付成功，扣减购物车，完成订单
							that.$u.post("payment_success", {
								openid: that.openid,
								orderIds: orderData.orderIds,
								cartIds: orderData.cartIds
							}).then(paymentSuccessRes => {});
							// 跳转到首页
							setTimeout(function() {
								uni.switchTab({
									url: "/pages/home/index"
								});
							}, 650);
						},
						fail: function(payRes) {
							uni.showToast({
								title: "已取消支付",
								icon: "none"
							});
							// 取消支付，删除订单、支付单
							that.$u.post("payment_fail", {
								openid: that.openid,
								orderIds: orderData.orderIds,
								paymentId: orderData.paymentId
							}).then(paymentFailRes => {});
						}
					});
				});
				
			}
		}
	}
</script>

<style lang="scss">
page{background: #f3f3f3;}

.sett_purse{color: #fa3534; font-size: 30rpx; position: relative; top: 2rpx;}
</style>
