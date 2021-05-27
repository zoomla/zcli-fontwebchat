<template>
	<view>
<!-- 		<view class="home_search">
			<u-search placeholder="检索字研所文献" :show-action="false" @search="toSearch" v-model="keyword"></u-search>
		</view> -->
		<card-swiper></card-swiper>
		<view class="index_well">
			<view @click="go_showIndex">
				<view class="index_well_icon"><i class="zi zi_paintbrush"></i></view>
				<view class="index_well_start">
				<strong>佶雅阁精展</strong>
				<span>当代瘦金珍品收藏</span>
				</view>
			</view>
			<view @click="goto('/pages/channel/biao')">
				<view class="index_well_icon"><i class="zi zi_slidersh"></i></view>
				<view class="index_well_start">
				<strong>古法装裱局</strong>
				<span>宣和神韵天水技传</span>
				</view>
			</view>
			<view @click="go_hui">
				<view class="index_well_icon"><i class="zi zi_schoolDesk"></i></view>
				<view class="index_well_start">
				<strong>西江会</strong>
				<span>凝聚文化人的智慧</span>
				</view>
			</view>
			<!-- <view @click="goto('/pages/channel/edu')"> -->
			<view @click="goto('/pages/channel/class?nid=53')">
				<view class="index_well_icon"><i class="zi zi_magic"></i></view>
				<view class="index_well_start">
				<strong>瘦金传习社</strong>
				<span>30天移魂法训练营</span>
				</view>
			</view>
		</view>
		<view class="index_listBox">
		<view class="index_list"  @tap="showDetail(items.GeneralID)" v-for="(items,list) in conlist" :key="list">
			<view class="index_list_pic">
			<img :src="items.TopImg" mode="widthFix"></image>
			</view>
			<view class="index_list_txt">
				<view class="index_list_txtTitle">{{items.Title}}</view>
				<view> {{items.CreateTime}}</view>
				</view>
		</view>
		<u-button class="index_more" @click="go_indexMore">阅读更多 +</u-button>
		</view>
		
		<view class="index_about">
			<view>关于江西字研所</view>
			<p>江西字研所&reg;是一家专业的字体设计、字库开发、软件研发机构。我们重在瘦金体传习、宋学研究、宣和裱与古法训诂小学的研究。本所先后承建海昏侯汉隶、逐浪新宋（六种规格）、海棠居刻本字，集文化传播、高端艺术展、瘦金体书法传播、书法培训等一体，是国家高新科技企业，分别在南昌、上海、北京设立研发中心，服务高端产业客户。</p>
		</view>
	<cart-footer></cart-footer>
	</view>
</template>
<script>
	import cardSwiper from "@/components/helang-cardSwiper/helang-cardSwiper"
	export default {
		data() {
			return {
				// topNavIndex:0,
				pageScrollTop:0,	// 页面滚动距离
				conlist:[],
				keyword: "", // 搜索关键字
			}
		},
		components:{
			cardSwiper
		},
		computed:{
			topNavStyle(){
				let r = this.pageScrollTop  / 100;
				return {
					"class":r>=0.85?'style2':'',
					// "style":`background-color: rgba(66, 185, 131,${r>=1?1:r});`
				}
			}
		},
		onLoad() {
		},
		onReady() {
			uni.setNavigationBarColor({
				frontColor: '#ffffff',
				backgroundColor: '#181619',
				animation: {
					duration: 400,
					timingFunc: 'easeIn'
				}
			})
		},
		// 页面滚动监听
		onPageScroll(e){
			this.pageScrollTop = Math.floor(e.scrollTop);
		},
		methods: {
			// 顶部导航改变 
			changeTopNav(e){
				this.topNavIndex = e.currentTarget.dataset.index
			},
			getConlist(){ // 调数据
				this.$u.get('content_list',{
					pnid:43,
					cpage:1,
					psize:5,
				}).then((res)=>{
					// console.log(res)
					for	(let i=0;i<res.result.length;i++){
						res.result[i].CreateTime=res.result[i].CreateTime.toString().replace(/T/g," ");
					}
					this.conlist=res.result;
				})
			},
			showDetail(itemid){  // 进入内容页
				uni.navigateTo({
					url:'/pages/home/enjoy_item?id='+itemid
				})
			},
			go_hui(){
				uni.navigateTo({ //进入西江会频道
					url:'/pages/channel/hui'
				})
			},
			go_showIndex(){
				uni.navigateTo({ //进入佶雅阁展
					url:'/pages/channel/zhan'
				})
			},		
			go_indexMore(){
				uni.switchTab({ //进入更多
					url:'/pages/home/live'
				})
			},	
			goto(url) { //通用clik链接方法
				uni.navigateTo({
					url:url
				})
			},
			toSearch(){  // 去搜索
				uni.navigateTo({
					url:`/pages/channel/search?keyword=${this.keyword}`  
				})
			},
		},
		onLoad(){
			this.getConlist()
		},
	}
</script>
<style lang="scss">
@import "style/home_card.scss";
page{background: #0F0F11; color: #ccc;}
	/* 标题栏 */
	.title{position: fixed; top: 0; left: 0; width: 100%; height: auto; padding-top: var(--status-bar-height); z-index: 10; color: rgba(255,255,255,0.8);
		&>view{height: 44px;}
		.box1{width: 60rpx;	margin: 0 40rpx; font-size: 36rpx;}
		.tab{
			&>view{/*margin: 0 30rpx;*/line-height: 64rpx; font-size: 36rpx; position: relative; letter-spacing: 0; transition: transform 0.3s ease-in-out 0s; transform: scale(1,1);
				&.active{color: rgba(255,255,255,1); transform: scale(1.15,1.15);}
			}
		}
		&.style2{color: #FFF;
			.tab{
				&>view{
					&.active{color: #FFF;}
				}
			}
		}
	}

</style>