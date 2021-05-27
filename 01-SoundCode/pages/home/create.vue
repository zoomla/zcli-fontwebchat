<template>
<view>
<view class="create_hero"></view>
<view class="create_0"></view>
<view class="create_list">
<view @tap="showDetail(items.GeneralID)" v-for="(items,list) in conlist" :key="list">
<text :style="{fontFamily:items.Subtitle}">{{items.Title}}</text>
<small>{{items.synopsis}}</small>
</view>
</view>
<view class="create_more">
<u-button @click="goto('/pages/webview/zfont-list')">卓越字库-逐浪而来 探索更多 +</u-button>
</view>
</view>
</template>

<script>
	export default {
		data() {
			return {
				conlist:[],
				aaaa:'red',
			}
		},
		onReady() {
			uni.setNavigationBarColor({
					frontColor: '#000000',
					backgroundColor: '#CCCCCC',
					animation: {
							duration: 400,
							timingFunc: 'easeIn'
					}
			})
		},
		methods: {
			getConlist(){
				this.$u.get('content_list',{
					nid:14
				}).then((res)=>{
					console.log(res)
					this.conlist=res.result;
				})
			},
			showDetail(itemid){
				uni.navigateTo({
					url:'/pages/home/enjoy_item?id='+itemid
				})
			},
			goto(url) { //通用clik链接方法
				uni.navigateTo({
						url:url
				})
			},
		},
		onLoad(){
			this.getConlist()
		},
	}
</script>
<style>
page{background: #303133;}
</style>
