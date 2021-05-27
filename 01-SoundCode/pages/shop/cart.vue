<template>
	<view>
		<view>
			<view class="cart_title">
				<view class="cart_title_che"></view>
				<view class="cart_title_img">商品图</view>
				<view class="cart_title_title">商品</view>
				<view class="cart_title_pro">数量</view>
				<view class="cart_title_xj">小计</view>
				<view class="cart_title_del">操作</view>
			</view>
			<view v-for="(item, index) in cartList" :key="index">
				<view class="cart_list">
					<view class="cart_list_che">
						<u-checkbox v-model="item.selected" value="true" size="40"></u-checkbox>
					</view>
					<view class="cart_title_imgx">
						<navigator :url="'../share/detail?id={{item.proid}}'" hover-class="navigator-hover">
							<u-image :src="item.proimg" width="100%" height="100%"></u-image>
						</navigator>
					</view>
					<view class="cart_list_title">
						<navigator :url="'../share/detail?id={{item.proid}}'" hover-class="navigator-hover">
							<view class="cart_list_title1">{{item.proname}}</view>
							<view class="cart_list_pri">{{item.FarePrice1}}</view>
						</navigator>
					</view>
					<view class="cart_list_pro">
						<u-number-box v-model="item.pronum" :min="1" @change="adjustNum(index)"></u-number-box>
					</view>
					<view class="cart_list_xj">{{item.AllMoney}}</view>
					<view class="cart_list_del" @click="delProduct(index)">删除</view>
				</view>
			</view>
		</view>
		<view class="cart_list_butt">
			<u-button @click="delProductRange" :custom-style="{background:'rgba(96,98,102, 1)', color:'#fff'}">删除所选</u-button>
		</view>
		<view id="fixedBottom">
			<view class="content">
				<view>
					<view>数量：<view class="red">{{selectedProCount()}}</view>
					</view>
					<view>总价：<view class="red money big">{{selectedProTotalFee()}}</view>
					</view>
				</view>
				<view>
					<!-- <view class="link" @click="chooseAddress()">选择地址</view> -->
					<view>
						<u-button @click="toPay" :custom-style="{background:'rgba(48,49,51, 1)', color:'#fff'}">去结算</u-button>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				openid: "",
				cartList: []
			}
		},
		onLoad() {
			const that = this;

			if (!that.$zoomla.isLogin()) {
				uni.showToast({
					title: '请先登录',
					icon: 'none',
					mask: true
				});
				return;
			}

			let mu = this.$store.state.vuex_user;
			that.openid = mu.openid;

			that.loadCart();
		},
		methods: {
			// 加载购物车
			loadCart() {
				const that = this;

				that.cartList.splice(0, that.cartList.length);
				that.$u.get("cart_list", {
					openid: that.openid
				}).then(res => {
					for (let i = 0; i < res.result.CartDT.length; i++) {
						let item = res.result.CartDT[i];
						item.selected = true;
						that.cartList.push(item);
					}

					that.EventBus.$emit("cartHaveChange");
				});
			},
			// 调整商品数量
			adjustNum(index) {
				let target = this.cartList[index];
				this.$u.post("cart_num", {
					id: target.id,
					num: target.pronum
				}).then(res => {
					/* 看似冗余的操作其实是在规避浮点数的精度问题，在没有更好的方案前请不要优化它 */
					target.AllMoney = (parseInt(target.FarePrice1 * 100) * target.pronum / 100);
				});
			},
			// 移除商品
			delProduct(index) {
				const that = this;

				uni.showModal({
					title: "提示",
					content: "确定要删除吗？",
					success: function(oprRes) {
						if (!oprRes.confirm) {
							return;
						}

						uni.showLoading({
							mask: true
						});

						let target = that.cartList[index];
						that.$u.post("cart_del", {
							id: target.id,
							openid: that.openid
						}).then(res => {
							if (res.retcode != 1) {
								uni.showToast({
									title: '删除失败',
									icon: 'none'
								});
								return;
							}

							uni.showToast({
								title: '删除成功',
								icon: 'none'
							});
							that.cartList.splice(index, 1);
							that.EventBus.$emit("cartHaveChange");
						});
					}
				});
			},
			// 批量移除商品
			delProductRange() {
				const that = this;

				uni.showModal({
					title: "提示",
					content: "确定要删除吗？",
					success: function(oprRes) {
						if (!oprRes.confirm) {
							return;
						}

						let ids = "";
						let prods = that.selectedProList();
						for (let i = 0; i < prods.length; i++) {
							const item = prods[i];
							ids += "," + item.id;
						}
						if (!ids) {
							return;
						}
						ids = ids.substr(1);

						that.$u.post("cart_del", {
							ids: ids,
							openid: that.openid
						}).then(res => {
							if (res.retcode != 1) {
								uni.showToast({
									title: '删除失败',
									icon: 'none'
								});
								return;
							}

							uni.showToast({
								title: '删除成功',
								icon: 'none'
							});
							that.loadCart();
						});
						//------------ success 结束
					}
				});
				//------------ delProductRange 结束
			},
			// 获取已选择的商品个数
			selectedProCount() {
				return this.selectedProList().length;
			},
			// 获取已选择商品的总价
			selectedProTotalFee() {
				let pros = this.selectedProList();
				let total = 0;
				for (let i = 0; i < pros.length; i++) {
					total += pros[i].AllMoney * 100;
				}
				return total / 100;
			},
			// 获取已选择的商品列表
			selectedProList() {
				let arr = [];
				for (let i = 0; i < this.cartList.length; i++) {
					const item = this.cartList[i];
					if (!item.selected) {
						continue;
					}
					arr.push(item);
				}
				return arr;
			},
			// 选择收货地址
			chooseAddress() {
				const that = this;
				wx.chooseAddress({
					success: function(address) {
						wx.setStorageSync("rece", address);
					},
					fail: function() {},
					complete: function() {}
				});
			},
			// 付款
			toPay() {
				const that = this;
				// 取得已选择的商品
				let proArr = that.selectedProList();
				if (proArr.length == 0) {
					uni.showToast({
						title: '请先选择商品',
						icon: 'none'
					});
					return;
				}
				let ids = "";
				for (let i = 0; i < proArr.length; i++) {
					ids += "," + proArr[i].id;
				}
				ids = ids.substr(1);

				// 跳转到结算页
				uni.navigateTo({
					url: "/pages/shop/getOrder?ids=" + ids
				});
			}
		}
	}
</script>

<style lang="scss">
.cart_title{display:flex; background: #eeeeee; text-align: center;
view{height:80rpx; line-height: 80rpx;}
}
.cart_title_che{width:8%;}
.cart_title_img{width:20%;}
.cart_title_title{width:20%;}
.cart_title_pro{width:30%;}
.cart_title_xj{width:10%;}
.cart_title_del{width:12%;}

.cart_list{display: flex;text-align: center; border-bottom: dotted 1rpx #ccc; padding-top: 20rpx; padding-bottom: 20rpx;}
.cart_list_che{width:8%;
u-checkbox{margin-left: 26rpx;}
}
.cart_title_imgx{width:20%; height:100rpx; padding-left: 5rpx; padding-right: 5rpx;
navigator{width:100%; height:100%;}
}
.cart_list_title{width:20%; padding-left: 5rpx; padding-right: 5rpx;}
.cart_list_pro{width:25%;
u-number-box{width:100%;}
}
.cart_list_xj{width:17%;}
.cart_list_del{width:10%;}
.cart_list_title1{font-size:23rpx; height:57rpx; overflow: hidden;}
.cart_list_pri{margin-top: 10rpx; font-size: 23rpx; color:#DF3232;}
.cart_list_pri::before{content: "\0A5";font-family: Arial, Helvetica, sans-serif;}
.cart_list_che,.cart_list_pro,.cart_list_xj,.cart_list_del{display: flex;align-content: center;justify-content: center;align-items: center;}
.cart_list_butt{margin-top: 10rpx; width:30%; font-size:20rpx;}

#fixedBottom {
	height: 128rpx;
	.content {
		display: flex;
		justify-content: space-between;
		align-items: center;
		position: fixed;
		z-index: 999;
		bottom: 0;
		left: 0;
		width: 100%;
		padding: 18rpx 12rpx;
		background: #fff;

		view,button {
			display: inline-block;
		}

		view:not(:first-child) {
			margin-left: 24rpx;
		}

		.link {
			color: rgb(29,153,255);
		}

		.red {
			font-size: 30rpx;
			color: red;
		}

		.money::before {
			content: "\0A5";
			font-family: Arial, Helvetica, sans-serif;
		}

		.money.big {
			font-weight: 700;
			font-size: 36rpx;
		}
	}
}
</style>
