<template>
	<view class="hui_index">
	
		<uni-swiper-dot :info="conlist" :current="current" field="content" :mode="mode">
			<swiper class="swiper-box" @change="change" >
				<swiper-item v-for="(item ,index) in conlist" :key="index">
					<view class="swiper-item">
						<!-- {{item.content}} -->
						<image :src="item.TopImg" class="swiper-item_img" mode="aspectFill" @click="swiperClick(item)" />
						<view class="hui_swiper_txt">{{item.Title}}</view>
					</view>
				</swiper-item>
			</swiper>
		</uni-swiper-dot>

		<view class="hui_listBox">		
		<view class="index_list"  @tap="showDetail(items.GeneralID)" v-for="(items,list) in conlist2" :key="list">
			<view class="index_list_pic">
			<img :src="items.TopImg" mode="widthFix"></image>
			</view>
			<view class="index_list_txt">
				<view class="index_list_txtTitle">{{items.Title}}</view>
				<view><u-icon name="eye" color="#2979ff" size="28"></u-icon> 2021/2/10</view>
				</view>
		</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			current: 0,
			mode: 'round',
			conlist:[],
			conlist2:[],
		}
		
	},
	methods: {
		change(e) {
			this.current = e.detail.current;
		},
		goConlist(){ //获取文章列表
			this.$u.get('content_list',{
				nid:49,
				cpage:1,
				psize:3,
			}).then((res)=>{
				// console.log(res)
				this.conlist=res.result;
			})
		},
		goConlist2(){ //获取文章列表
			this.$u.get('content_list',{
				nid:49,
			}).then((res)=>{
				// console.log(res)
				this.conlist2=res.result;
			})
		},
			showDetail(itemid){  // 进入内容页
				uni.navigateTo({
					url:'/pages/home/enjoy_item?id='+itemid
				})
			},		
		
		
	},
	onLoad(){
		this.goConlist(),
		this.goConlist2()
	},
	
}
</script>

<style>
page{background:#F5F7F9;color: #333;	}
</style>
