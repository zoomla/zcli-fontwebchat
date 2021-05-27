<template>
	<view class="biao">
		<view class="biao_well">
		<view class="biao_well_title">装璜者书之司命也</view>
		<view class="biao_well_author">-明·周嘉胄《装潢志》</view>
		<i class="zi zi_pathDown zi_load biao_well_more"></i>
		</view>

		<view class="biao_con">
			<view class="biao_con_title">装裱形式</view>
		<u-grid :col="6">
				<u-grid-item @click="goto_nid('2')">
					<u-icon name="photo" :size="46"></u-icon>
					<view class="grid-view">镜片</view>
				</u-grid-item>
				<u-grid-item @click="goto_nid('3')">
					<u-icon name="attach" :size="46"></u-icon>
					<view class="grid-view">扇面</view>
				</u-grid-item>
				<u-grid-item>
					<u-icon name="pushpin" :size="46"></u-icon>
					<view class="grid-view">立轴</view>
				</u-grid-item>
				<u-grid-item>
					<u-icon name="gift" :size="46"></u-icon>
					<view class="grid-view">册页</view>
				</u-grid-item>
				<u-grid-item>
					<u-icon name="moments" :size="46"></u-icon>
					<view class="grid-view">手卷</view>
				</u-grid-item>
				<u-grid-item>
					<u-icon name="order" :size="46"></u-icon>
					<view class="grid-view">装框</view>
				</u-grid-item>
			</u-grid>
			<view class="biao_con_title">工艺不凡</view>
		<u-grid :col="6">
				<u-grid-item>
					<u-icon name="chat" :size="46"></u-icon>
					<view class="grid-view">制糊</view>
				</u-grid-item>
				<u-grid-item>
					<u-icon name="bag" :size="46"></u-icon>
					<view class="grid-view">托绫</view>
				</u-grid-item>
				<u-grid-item>
					<u-icon name="star" :size="46"></u-icon>
					<view class="grid-view">画心</view>
				</u-grid-item>
				<u-grid-item>
					<u-icon name="list-dot" :size="46"></u-icon>
					<view class="grid-view">上壁</view>
				</u-grid-item>
				<u-grid-item>
					<u-icon name="checkmark" :size="46"></u-icon>
					<view class="grid-view">扶平</view>
				</u-grid-item>
				<u-grid-item>
					<u-icon name="bookmark" :size="46"></u-icon>
					<view class="grid-view">方裁</view>
				</u-grid-item>
			</u-grid>
			
			<view class="biao_con_title">有何不同</view>
		<u-grid :col="6">
				<u-grid-item>
					<u-icon name="edit-pen" :size="46"></u-icon>
					<view class="grid-view">原料</view>
				</u-grid-item>
				<u-grid-item>
					<u-icon name="file-text-fill" :size="46"></u-icon>
					<view class="grid-view">工艺</view>
				</u-grid-item>
				<u-grid-item>
					<u-icon name="grid" :size="46"></u-icon>
					<view class="grid-view">团队</view>
				</u-grid-item>
				<u-grid-item>
					<u-icon name="eye" :size="46"></u-icon>
					<view class="grid-view">见解</view>
				</u-grid-item>
				<u-grid-item>
					<u-icon name="integral" :size="46"></u-icon>
					<view class="grid-view">鉴赏</view>
				</u-grid-item>
				<u-grid-item>
					<u-icon name="coupon" :size="46"></u-icon>
					<view class="grid-view">证书</view>
				</u-grid-item>
			</u-grid>
			<u-button size="default" @click="goto('/pages/shop/shop')"><i class="zi zi_Fucircle zi_spin"></i> 我要装裱</u-button>
		</view>
		
		<view class="biao_know">
			<view class="biao_know_title" @click="goto_nid('11')">装裱杂谈</view>
			<u-grid :col="1">
				<u-grid-item>
				<view @tap="showDetail(items.GeneralID)" v-for="(items,list) in conlist" :key="list">{{items.Title}}</view>					
				</u-grid-item>				
				</u-grid>
			<view class="biao_know_title" @click="goto_nid('12')">装裱知识</view>
			<u-grid :col="1">
				<u-grid-item>
				<view @tap="showDetail(items.GeneralID)" v-for="(items,list) in conlist2" :key="list">{{items.Title}}</view>	
				</u-grid-item>				
				</u-grid>
			<view class="biao_know_title" @click="goto_nid('33')">古藉修复</view>
			<u-grid :col="1">
				<u-grid-item>
				<view @tap="showDetail(items.GeneralID)" v-for="(items,list) in conlist3" :key="list">{{items.Title}}</view>	
				</u-grid-item>				
				</u-grid>
		</view>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				conlist:[],
				conlist2:[],
				conlist3:[],
			}
		},
		onReady() {
			uni.setNavigationBarColor({
					frontColor: '#ffffff',
					backgroundColor: '#848FA3',
					animation: {
							duration: 400,
							timingFunc: 'easeIn'
					}
			})
		},		
		methods: {
			goto_nid(nid) { //通用资讯栏目clik链接方法+参数
							uni.navigateTo({
									url:'/pages/channel/class?nid='+nid
							})
						},
			goto_shopNid(nid) { //通用栏目clik链接方法+参数
							uni.navigateTo({
									url:'/pages/member/shop_class?nid='+nid
							})
						},
			goto(url) { //通用clik链接方法+参数
							uni.navigateTo({
									url:url
							})
						},
			showDetail(itemid){
				uni.navigateTo({
					url:'/pages/home/enjoy_item?id='+itemid
				})
			},						
		getConlist(){ //获取文章列表
				this.$u.get('content_list',{
					nid:12,
					psize:6,
				}).then((res)=>{
					// console.log(res) //输出结果
					this.conlist=res.result;
				})
			},					
		getConlist2(){ //获取文章列表
				this.$u.get('content_list',{
					nid:33,
					psize:6,
				}).then((res)=>{
					// console.log(res) //输出结果
					this.conlist2=res.result;
				})
			},					
		getConlist3(){ //获取文章列表
				this.$u.get('content_list',{
					nid:11,
					psize:6,
				}).then((res)=>{
					// console.log(res) //输出结果
					this.conlist3=res.result;
				})
			},						
		},
		onLoad(){
			this.getConlist()
			this.getConlist2()
			this.getConlist3()
		}
	}
</script>

<style>
page{ background:#10141D;}
.grid-view {font-size: 28rpx;margin-top: 4rpx;color: $u-type-info;}
</style>
