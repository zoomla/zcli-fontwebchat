<template>
	<view>
		  <view class="ucenter_info">
				<view class="ucenter_infoLeft">
		    <image v-if="vuex_token" :src="safeFaceUrl(vuex_user.userFace)" mode="aspectFit"></image>
				<image v-else src="//www.sjtzj.com/UploadFiles/Images/weixin/a2.jpg"></image>
				<view v-if="vuex_token"> {{vuex_user.honeyName}}
				<view class="ucenter_money">
					<button class="mini-btn" type="default" size="mini" plain="true" @click="$u.route('/pages/member/money')">¥{{vuex_user.purse.toFixed(2)}}元</button>
					<button class="mini-btn" type="default" size="mini" plain="true" @click="$u.route('/pages/member/pay')">点击充值</button>
					</view>
				</view>
		    <navigator v-else  url="../member/wxauth">书友访客<text class="u-font-13">(点击登录)</text></navigator>
				<!-- <navigator v-else url="../member/wxauth">去登录</navigator> -->
				</view>
<!-- 				<view class="ucenter_login">
				<image :src="vuex_user.userFace" mode="aspectFit"></image>
				<button v-if="vuex_token" @click="loginout">退出登录</button>
				<navigator v-else url="../member/wxauth">去登录</navigator>
				</view> -->
		<!-- <button size="mini" open-type="share" plain="true">分享助力</button> -->
		<view class="ucenter_infoR">
		<button size="mini" type="default" open-type="contact" plain="true"><u-icon name="server-man"></u-icon>在线客服</button>
		<u-icon name="setting" @click="uClick('/pages/member/setting')"></u-icon>
		</view>
		  </view>
			<view class="ucenter_open">
			<view class="ucenter_open_sys">
				<view>开通字研所VIP会员</view>
				<view>每月仅需30元/畅享16项特权</view>
			</view>
			<u-button type="success" @click="goto('/pages/shop/shop')">立即开通</u-button>
			</view>
			
		<view class="ucenter_box">
		<view class="ucenter_box_4">
			<view class="ucenter_box_4list" @click="uClick('/pages/member/fav')">
				<view><i class="zi zi_loves"></i></view>
			<text>我的收藏</text>
			</view>	
			<view class="ucenter_box_4list" @click="uClick('/pages/member/myorder')">
				<view><i class="zi zi_gem"></i></view>
			<text>商城订单</text>
			</view>					
			<view class="ucenter_box_4list">
				<view><i class="zi zi_clipboardlist"></i></view>
			<text>装裱订单</text>
			</view>
			<view class="ucenter_box_4list">
				<view><i class="zi zi_SquareTable"></i></view>
			<text>书法订单</text>
			</view>
		</view>	
		
		<view @click="signIn"><u-icon name="checkbox-mark"></u-icon>点我签到</view>
		<view @click="$u.route('/pages/member/jifen')"><u-icon name="coupon"></u-icon>当前共有{{vuex_user.point || "???"}}积分</view>
			<u-cell-group>
				<!-- <u-cell-item icon="setting-fill" title="个人设置" @click="uClick('/pages/channel/biao')"></u-cell-item> -->
				<u-cell-item icon="shopping-cart" title="装裱商城" value="古法手工技艺高"  @click="goto('/pages/shop/shop')"></u-cell-item>
				<u-cell-item icon="thumb-up" title="文玩柜台" value="精选私藏真无二" @click="goto('/pages/shop/shop')"></u-cell-item>
				<u-cell-item icon="tags" title="参观预约" value="佶雅阁书画艺术馆" @click="goto('/pages/channel/yuyue')"></u-cell-item>
				<u-cell-item icon="bookmark" title="字研理念" value="走近江西字研所" @click="open"></u-cell-item>
				<u-cell-item icon="info-circle" title="关于我们" value="创业团队的故事" @click="open2"></u-cell-item>
				<u-cell-item icon="phone" title="联系我们" value="一个电话上门揽裱" @click="call_phone(13177777714)"></u-cell-item>
			</u-cell-group>
			<view class="ucenter_loginOut">
			<button type="default" plain="true" v-if="vuex_token" @click="loginout">退出登录</button>
			<!-- <navigator v-else url="../member/wxauth">去登录</navigator> -->
			</view>
		<view class="ucenter_copyright">
			24小时全天侯客服热线：13177777714<br/>
			基于<text @click="goto('/pages/webview/z01-com')">Zoomla!逐浪CMS</text>内核  <text @click="goto('/pages/webview/73ic-com')">去上云73ic.com</text>驱动<br/>
			<text @click="goto('/pages/webview/sjtzj-com')">www.sjtzj.com 瘦金体之家</text>官网支持 <br>
			字研所、佶雅阁、逐浪是注册商标<br>
		</view>			
		</view>
		
		<u-modal v-model="show" :title-style="{color: 'red'}" :title="title">
					<view class="slot-content">
						<rich-text :nodes="content"></rich-text>
					</view>
				</u-modal>
		<u-modal v-model="show2" :title-style="{color: 'red'}" :title="title2">
					<view class="slot-content">
						<rich-text :nodes="content2"></rich-text>
					</view>
				</u-modal>
				
	</view>
</template>
<script>
	export default {
		data() {
			return {
				show: false,
				title:'字者新生-中国文字逐浪造！',
				content: `
					字者，新生，代表一切美好的事物孕育。<br>
					研者，摩者是也，从手从石，创造无限。<br><br>
					字研所小程序，是由国内领先的字库与软件研发机构-逐浪团队打造的官方应用，服务于广大用户，推送最新资讯。<br>
					同时还提供专业的瘦金体书法研究、线下书法展、装裱、文房用品交易、艺术品收藏等服务，感谢您的访问，并期待您将【江西字研所】小程序介绍推送给更多的书友，谢谢！
				`,
				show2:false,
				title2:'创立18年的专业字库智造团队',
				content2:`<img src="https://www.sjtzj.com/UploadFiles/Images/weixin/team.jpg" width="100%"><strong>笨、难、丑、朴,专注做中国互联网的鲁班。</strong><br>
				南昌、上海、北京，18年立业，国内首个开放字库软件的技术团队，先后服务中化集团、中国电建、水电基础局、远东宏信、中国金茂以及军工、世界五百强。<br><br>
				<strong>软件公司的公司</strong><br>国内唯一具备字库、图标、图库、CMS、OA、ERP全闭环web呈现知识独立自主知识产权的服务商，并建立<u>国内唯一从字库生产、手工装裱、古藉修复、文字创作、软件研发一体的全产业链文字机构</u>，官网www.ziti163.com。
				`,
			};
		},
		methods:{
			getUserInfo(e){
				console.log(1121)
				// uni.login({
				// 	success: (res) => {
				// 		console.log(res)
				// 	}
				// })
				// console.log(e)
				if(uni.getUserProfile){
					console.log('ok')
				}else{
					console.log('no')
				}
				uni.getUserProfile({
					desc:'登录',
					success:(res)=>{
						console.log(res,2)
					},
					fail:(fail)=>{
						console.log(fail,11)
					}
				})
			},
			login(){
				this.$zoomla.isLogin();
			},
			loginout(){
				uni.showLoading();
				this.$u.vuexReset();
				this.$zoomla.loginOut();
				this.EventBus.$on("cartHaveChange");
			},
			uClick(url,tab=false){ //未登录则跳到登录页，否则进入目标页
				if(!this.$zoomla.isLogin()){
					return;
				}
				if(tab){
					uni.switchTab({
						url: url,
						animationDuration: 200,
						animationType: 'pop-in'
					})
				}else{
					uni.navigateTo({
						url: url,
						animationDuration: 200,
						animationType: 'pop-in'
					})
				}			
			},
			goto(url) { //通用clik链接方法
				uni.navigateTo({
						url:url
				})
			},
			call_phone(phone){
				uni.makePhoneCall({	//拔打电话
				    phoneNumber:'' + phone
				});
			},
			signIn() { // 用户签到
				if(!this.$zoomla.isLogin()){
					return;
				}
				const that = this;
				let mu = this.$store.state.vuex_user;
				// 查找签到积分设置
				that.$u.get("user_score_setting").then(settingRes => {
					if (settingRes.result <= 0) {
						return; // 未设置签到积分
					}
					// 查找积分日志
					that.$u.get("user_exp_list_log", {
						uid: mu.userId
					}).then(logListRes => {
						for (let i = 0; i < logListRes.result.length; i++) {
							const item = logListRes.result[i];
							if (new Date(item.HisTime).toLocaleDateString() == new Date().toLocaleDateString()) {
								uni.showToast({
									title: '今日已签到',
									icon: 'none'
								});
								return; // 今日已签到
							}
						}
						// 操作积分
						that.$u.post("user_exp_update", {
							uid: mu.userId,
							exp: settingRes.result
						}).then(updateRes => {
							// 记录日志
							let logData = {
								UserID: mu.userId,
								score: settingRes.result,
								score_before: mu.point,
								HisTime: this.$u.timeFormat(new Date(), "yyyy-mm-dd hh:MM:ss"),
								Operator: mu.userId,
								Detail: "签到",
								ScoreType: 3
							};
							that.$u.post("user_exp_update_log", {
								model:JSON.stringify(logData)
							}).then(logRes => {});
							// 更新用户信息
							this.$zoomla.getMemberInfo()
							//===================
						}); // 操作积分 end
						//===================
					}); // 查找积分日志 end
					//===================
				}); // 查找签到积分设置 end
			},
			safeFaceUrl(src) {
				if (src.startsWith("http")) {
					return src;
				}
				return this.$u.http.config.baseUrl + src;
			},
			open() {
							this.show = true;
						},
			open2() {
							this.show2 = true;
						}
		},
		onLoad() {
			/**
			 * 此处本意在页面加载时刷新用户信息，但是接口`user_info`返回的信息中并没有`openid`字段，
			 * 而此处代码的逻辑则是：获取到用户的最新信息 -> 用获取到的信息完全覆盖已有的信息，
			 * 这就导致之后所有依赖于存放在`vuex_user`中的`openid`的功能出现问题
			 * 建议解决方案：
			 *   1、将下方注释（最快，最简单，但是在后台更新了用户信息后前台需要重新登录才可以获取到最新信息）
			 *   2、修复后台接口`user_info`返回数据不完全的问题
			 * 20210515问题解决：更换接口为`user_get`实现
			 */
			this.$zoomla.getMemberInfo()

			console.log(this.$store.state.vuex_version)
			uni.setNavigationBarColor({
				frontColor: '#ffffff',
				backgroundColor: '#B7B8CC',
				animation: {
					duration: 400,
					timingFunc: 'easeIn'
				}
			});
		}
	}
</script>
<style lang="scss">
page{background:#B7B8CC;color: #ccc;}
.slot-content {font-size: 28rpx;color: $u-content-color;padding-left: 30rpx; margin-top: 20rpx;
}
</style>
