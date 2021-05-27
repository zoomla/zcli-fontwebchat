<template>
	<view class="enjoy_item">
		<view class="enjoy_item_h1">{{article.title}} </view>
		<view class="enjoy_item_about">
		<text class="mr_10rpx">
			<text v-if="isStar" @click="unstar"><u-icon name="star-fill"></u-icon>已收藏</text>
			<text v-else @click="star"><u-icon name="star"></u-icon>收藏</text>
		</text>
		{{article.createTime}}
		</view>
		<u-parse class="enjoy_item_con" :html="article.content"></u-parse>
		<view class="enjoy_item_any" @click="$u.route('/pages/channel/biao')">
			<text>精装精裱找字研社</text> <br>中国独家从字库研发到装裱全产业链机构</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				article:{},
				starReqData: {},
				isStar: false,
			}
		},
		onLoad(options) {
			const that = this;
			let mu = this.$store.state.vuex_user;

			that.$u.get("content_get",{
				id:options.id
			}).then(res=>{
				if(res.retcode != 1){
					return;
				}
				let data=res.result[0];
				data.createTime=data.createTime.toString().replace(/T/g," ");
				that.article=data;
				for (let i=1;i<res.result.length;i++){
					that.article.content += res.resuslt[i].content;
				}
				uni.setNavigationBarTitle({
					title:that.article.title
				})
			});

			that.starReqData = {
				openid: mu.openid,
				id: options.id,
				type: 1
			};

			that.$u.get("user_star_is", that.starReqData).then(res => { that.isStar = res.result != 0; });
		},
		methods: {
			// 收藏
			star() {
				const that = this;
				if (!this.$zoomla.isLogin()) {
					return;
				}

				that.$u.post("user_star_add", that.starReqData).then(res => { that.isStar = true; });
			},
			// 取消收藏
			unstar() {
				const that = this;
				if (!this.$zoomla.isLogin()) {
					return;
				}

				that.$u.post("user_star_del", that.starReqData).then(res => { that.isStar = false; });
			}
		},
		onReady() {
			uni.setNavigationBarColor({
				frontColor: '#ffffff',
				backgroundColor: '#0F0F11',
				animation: {
					duration: 400,
					timingFunc: 'easeIn'
				}
			})
		},		
	}
</script>
<style>
page{background:#0F0F11;color: #a3a3a3;	}
</style>
