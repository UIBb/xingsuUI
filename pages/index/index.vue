<template>
	<view class="index-ov">
		<!-- 本地没有存触发引导添加到我的小程序 -->
		<view class="frist-notic" v-if="is_frist">
			点击右上角“●●●”添加到我的小程序
		</view>

		<view style="height: 400upx;">
			<image class="fengrui-img" src="../../static/index/333.svg" mode="aspectFill"></image>
			<!-- 分类菜单 -->
			<view :class="'menu_scroll_top_' + [topBar]">
				<view class="menu-scroll">
					<scroll-view class="" scroll-x="true">
						<view class="menu-scroll-leat">
							<view :class="active == index ? 'active_menu_scroll_leat_font':'menu_scroll_leat_font' "
								v-for="(menu,index) in menusLsit" :key="index" @tap="menuTap(index)">
								{{ menu.name }}
							</view>
						</view>
					</scroll-view>
				</view>
			</view>
		</view>

		<!-- 判断是否是第一个选项卡 -->
		<block v-if="active === 0">
			<!-- 轮播图 -->
			<view class="content-all" v-if="is_data_rotation">
				<swiper class="swiper-box" @change="change" :autoplay="autoplay" :interval="interval"
					:duration="duration">
					<swiper-item v-for="(item, index) in rotation" :key="item.id" @tap="openSwipre(item.id)">
						<view class="swiper-item">
							<image class="fengrui-img" :src="item.thumbnailurl" mode="widthFix" />
						</view>
					</swiper-item>
				</swiper>
			</view>

			<!-- 导航区域 -->
			<view class="Vajra-bg">
				<view class="Vajra">
					<block v-for="(valist,index) in vajra" :key="index">
						<view class="Vajra-list" v-if="valist.type != 'xs_program'" @tap="xs_default(index)">
							<view class="Vajra-list-img">
								<image class="fengrui-img" :src="valist.himg" mode="aspectFit"></image>
							</view>
							<view class="Vajra-list-font">
								{{ valist.title }}
							</view>
						</view>
						<!-- #ifdef MP-WEIXIN -->
						<view class="Vajra-list" v-if="valist.type == 'xs_program'">
							<navigator :path="valist.url" hover-class="none" open-type="navigate" :app-id="valist.appid"
								target="miniProgram">
								<view class="Vajra-list-img">
									<image class="fengrui-img" :src="valist.himg" mode="aspectFit"></image>
								</view>
								<view class="Vajra-list-font">
									{{ valist.title }}
								</view>
							</navigator>
						</view>
						<!-- #endif -->
					</block>

				</view>
				<!-- 海报 -->
				<view class="poster">
					<view class="poster-imgs">
						<image class="fengrui-img" src="../../static/index/poster.svg" mode="aspectFit"></image>
					</view>
					<view class="poster-text">
						心念所至，生万千喜欢
					</view>
					<button class="poster-btn" @tap="posterTap()">分享</button>
				</view>
			</view>

			<!-- #ifdef MP-WEIXIN -->
			<!--关注微信公众号组件添加组件 -->
			<view style="margin: 20rpx;">
				<official-account></official-account>
			</view>
			<!-- #endif -->

			<!-- 首页公告 -->
			<view class="notice-flex">
				<view class="notice-img">
					<image class="fengrui-img" src="../../static/index/notice.png" mode=""></image>
				</view>
				<uni-notice-bar class="notice-fr-title" scrollable="true" :color="color" single="true" :speed="speed"
					:text="about_center[0].fr_notice"></uni-notice-bar>
			</view>

			<!-- #ifdef MP-WEIXIN -->
			<view v-if="is_data_focuList">
				<!-- 焦点标题 -->
				<view class="titel-vi">
					<view class="titel-vi-q">
						<view class="titel-vi-img">
							<image class="fengrui-img" src="../../static/index/title-img.svg" mode="aspectFill"></image>
						</view>
						<view class="titel-vi-font">焦点</view>
					</view>
					<view class="titel-vi-right" @tap="wxMoreTap()">
						查看更多
					</view>
				</view>

				<!-- 焦点滑动区域 -->
				<view class="swiper-list">
					<scroll-view class="" scroll-x="true">
						<view class="scroll-view-item-list" v-for="(wxlist,index) in focuList" :key="index"
							@tap="scrlistTap(wxlist.hurl)">
							<view class="scroll-view-item-h">
								<view class="scroll-view-item-h-img">
									<image class="fengrui-img" :src="wxlist.himg" mode="widthFix"></image>
								</view>
							</view>
							<view class="scroll-view-item-h-title">
								{{ wxlist.title }}
							</view>
							<view class="scroll-view-item-h-describe">
								{{ wxlist.describe }}
							</view>
						</view>
					</scroll-view>
				</view>
			</view>
			<!-- #endif -->
		</block>

		<!-- 焦点标题 -->
		<view class="titel-vi" v-if="active === 0">
			<view class="titel-vi-q">
				<view class="titel-vi-img">
					<image class="fengrui-img" src="../../static/index/title-img2.svg" mode="aspectFill"></image>
				</view>
				<view class="titel-vi-font">最新文章</view>
			</view>
			<view class="titel-vi-right" @tap="categoryNew()">
				查看更多
			</view>
		</view>

		<!-- 数据列表 -->
		<view class="postlist-bg" v-if="active === 0">
			<block v-for="(newpos,index) in newPost" :key="index">
				<view class="menu-scroll-list" @tap="newPostTap(newpos.id)" v-if="about_center[0].index_style != 2">
					<view class="menu-scroll-list-img" v-if="newpos.thumbnailurl == null ">
						<image class="fengrui-img" :src="about_center[0].xs_posts_img" mode="aspectFill"></image>
					</view>
					<view class="menu-scroll-list-img" v-else>
						<image class="fengrui-img" :src="newpos.thumbnailurl" mode="aspectFill"></image>
					</view>
					<view class="menu-scroll-list-right">
						<view class="menu-scroll-list-h">
							{{ newpos.title.rendered }}
						</view>
						<view class="menu-scroll-list-describe">
							<view>{{ newpos.date }}</view>
						</view>
					</view>
				</view>

				<view class="list-one-w" @tap="newPostTap(newpos.id)" v-if="about_center[0].index_style == 2">
					<view class="list-one-img" v-if=" newpos.thumbnailurl == null ">
						<image class="fengrui-img" mode="aspectFill" :src="about_center.xs_posts_img"></image>
					</view>
					<view class="list-one-img" v-else>
						<image class="fengrui-img" mode="aspectFill" :src="newpos.thumbnailurl"></image>
					</view>
					<view class="list-one-data-w">
						<view class="list-one-title">
							{{ newpos.title.rendered}}
						</view>
						<view class="list-ona-abstract">
							<rich-text :nodes="newpos.excerpt.rendered"></rich-text>
						</view>
					</view>
				</view>

				<!-- #ifdef MP-WEIXIN -->
				<block v-if="about_center.length>0">
					<view style="width: 100%;">
						<ad v-if="(index +1) % 5 == 0" :unit-id="about_center[0].wx_banner"></ad>
					</view>
				</block>
				<!-- #endif -->

				<!-- #ifdef MP-QQ -->
				<block v-if="about_center.length>0">
					<view style="width: 100%;">
						<ad v-if="(index +1) % 5 == 0" :unit-id="about_center[0].qq_banner"></ad>
					</view>
				</block>
				<!-- #endif -->
			</block>
		</view>

		<!-- 首页分类 -->
		<block v-if="active != 0">
			<view class="muen-tow-list-one" v-if="about_center[0].index_style != 2">
				<view v-for="(newpos,index) in newPost" :key="index" @tap="newPostTap(newpos.id)">
					<view class="menu-scroll-list">
						<view class="menu-scroll-list-img">
							<block>
								<image class="fengrui-img" :src="newpos.thumbnailurl" mode="aspectFill"></image>
							</block>
						</view>
						<view class="menu-scroll-list-right">
							<view class="menu-scroll-list-h">
								{{ newpos.title.rendered }}
							</view>
							<view class="menu-scroll-list-describe">
								<view>{{ newpos.date }}</view>
							</view>
						</view>
					</view>

					<!-- #ifdef MP-WEIXIN -->
					<block v-if="adLsit.length>0">
						<ad v-if="(index +1) % 5 == 0" :unit-id="adLsit[3].adid" ad-type="video" ad-theme="white"></ad>
					</block>
					<!-- #endif -->

					<!-- #ifdef MP-QQ -->
					<block v-if="qqAdlsit.length>0">
						<ad v-if="(index +1) % 5 == 0" :unit-id="qqAdlsit[0].adid" type="card"></ad>
					</block>
					<!-- #endif -->
				</view>
			</view>

			<view style="margin: 20upx 30upx;" v-if="about_center[0].index_style == 2">
				<view v-for="(newpos,index) in newPost" :key="index" @tap="newPostTap(newpos.id)">
					<view class="list-one-w">
						<view class="list-one-img">
							<image class="fengrui-img" mode="aspectFill" :src="newpos.thumbnailurl"></image>
						</view>
						<view class="list-one-data-w">
							<view class="list-one-title">
								{{ newpos.title.rendered}}
							</view>
							<view class="list-ona-abstract">
								<rich-text :nodes="newpos.excerpt.rendered"></rich-text>
							</view>
						</view>
					</view>

					<!-- #ifdef MP-WEIXIN -->
					<block v-if="adLsit.length>0">
						<ad v-if="(index +1) % 5 == 0" :unit-id="adLsit[3].adid" ad-type="video" ad-theme="white"></ad>
					</block>
					<!-- #endif -->

					<!-- #ifdef MP-QQ -->
					<block v-if="qqAdlsit.length>0">
						<ad v-if="(index +1) % 5 == 0" :unit-id="qqAdlsit[0].adid" type="card"></ad>
					</block>
					<!-- #endif -->
				</view>
			</view>
		</block>

		<!-- 文章列表没有数据 -->
		<view class="no-list-data" v-if="no_list_data">
			--我的底线就到这里了--
		</view>

		<!-- 遮照层 -->
		<view class="popup-view" v-if="is_popup">
			<view class="popup-about">
				<view class="popup-img">
					<!-- #ifdef MP-WEIXIN -->
					<image class="fengrui-img" :src="about_center[0].posterdata" mode="aspectFill"></image>
					<!-- #endif -->
					<!-- #ifdef MP-QQ -->
					<image class="fengrui-img" :src="about_center[0].qq_posterdata_copy" mode="aspectFill"></image>
					<!-- #endif -->
				</view>
				<view class="popup-btn">
					<button class="popup-fiend" @tap="popupCustomer()">复制客服号</button>
					<button class="popup-phothop" @tap="popupSever()">保存相册</button>
				</view>
				<view class="popup-close" @tap="popupExit()">
					<image class="fengrui-img" src="../../static/index/close.png" mode="aspectFill"></image>
				</view>
			</view>
		</view>

	</view>

</template>

<script>
	// 引入域名和自定义php文件
	import {
		API
	} from '@/components/api.js';
	import {
		Getwatch
	} from '@/components/api.js';
	import {
		Getdstrict
	} from '@/components/api.js';
	import uniNoticeBar from '@/components/uni-notice-bar/uni-notice-bar.vue';
	let interstitialAd = null;
	let InterstitialAd = null;
	export default {
		components: {
			uniNoticeBar
		},
		data() {
			return {
				// 引导添加我的小程序
				is_frist: false,

				// 公告轮播
				speed: 50,
				background_color: '#ffffff',
				color: '#807C7C',

				// 轮播图
				rotation: [],
				autoplay: true,
				interval: 2000,
				duration: 500,
				current: 0,
				mode: 'round',
				dotsStyles: {
					backgroundColor: 'rgba(255, 255, 255,1)',
					border: '1px rgba(255, 255, 255,1) solid',
					color: '#fff',
					selectedBackgroundColor: 'rgba(50, 144, 224, 1)',
					selectedBorder: '1px rgba(50, 144, 224, 1) solid'
				},

				// 导航区域
				vajra: [],

				// 焦点
				focusList: [],

				// 最新文章数据
				newPost: [],
				page: 1,


				// 文章没有数据
				no_list_data: false,
				sollrTop: '',

				//首页菜单
				active: 1,
				topBar: 'one',

				// 首页分类
				menusLsit: [],


				// #ifdef MP-WEIXIN

				focuList: [],
				// #endif

				// 基本数据
				about_center: [],


				// 局部控制php文件传入的参数为空的时候隐藏组件
				// 轮播图
				is_data_rotation: true,
				is_data_focuList: true,
				BarHeight: this.BarHeight,

				// 分享海报
				is_popup: false,

				// 首页限制分类id 
				exclude: '',
			}
		},
		onShow() {

		},
		onLoad() {
			// 自定义php接口请求
			this.Getres();

			// 界面打开 加载自定义标题高度函数
			this.topMenu();

			// 引导添加我的小程序
			this.fristNotice();

			// 微信分享盆友圈
			//#ifdef MP-WEIXIN
			wx.showShareMenu({
				withShareTicket: true,
				menus: ['shareAppMessage', 'shareTimeline']
			})
			//#endif

			//条件编译 QQ小程序分享
			//#ifdef MP-QQ
			qq.showShareMenu({
				showShareItems: ['qq', 'qzone', 'wechatFriends', 'wechatMoment']
			})
			//#endif

		},
		onShow() {
			if (interstitialAd) {
				/* 建议放在需要展示插屏广告的时机执行 */
				InterstitialAd.show().catch((err) => {
					console.error('show', err)
				})
			}
		},

		// 监听滑动
		onPageScroll: function(e) {
			// 判断型号 显示不同标题栏高度
			var that = this;
			if (e.scrollTop > 120) {
				that.topBar = 'tow';
			} else {
				that.topBar = 'one';
			}
		},

		// 下拉刷新
		onPullDownRefresh() {
			this.Getres();
			setTimeout(function() {
				uni.stopPullDownRefresh();
			}, 1000);
		},

		// 监听触底----上啦刷新
		onReachBottom() {
			this.page = this.page + 1;
			this.listPosts();
		},

		// 分享好友配置
		onShareAppMessage(res) {
			var that = this;

			console.log(that.XcxMessage)
			if (res.from === 'button') { // 来自页面内分享按钮
				console.log(res.target)
			}
			return {
				title: that.about_center[0].share_title,
				imageUrl: that.about_center[0].share_title_url,
				path: '/pages/index/index'
			}
		},

		methods: {
			// 自定义导航高度
			topMenu: function() {
				var that = this;
				uni.getSystemInfo({
					success: function(res) {
						// console.log(res.statusBarHeight);
						// console.log(res.model);
						that.BarHeight = res.statusBarHeight;
					}
				});
			},
			// 自定义php接口请求
			Getres: function() {
				var that = this;
				uni.request({
					url: Getwatch,
					success: (res) => {
						// 请求焦点
						that.focuList = res.data[0];
						//#ifdef MP-WEIXIN
						// 焦点文章
						if (res.data[0] == '') {
							console.log('您没有配置焦点文章')
							that.is_data_focuList = false
						} else {
							that.focuList = res.data[0].slice(0, 3);
							that.is_data_focuList = true;
						}
						//#endif
					}
				});
				// 首页金刚区域
				uni.request({
					url: Getdstrict,
					success: (res) => {
						console.log(res.data)
						that.vajra = res.data[0];
					}
				});

				// 请求基本配置数据
				uni.request({
					url: API + '/wp-json/wp/v2/fengrui',
					success: (res) => {
						that.about_center = res.data;

						// 轮播数据获取
						that.lunbo();

						// 加载插屏弹窗
						that.getWxchaAd();

						// 首页顶部菜单
						this.menuTap(0);

						// 紧急公告弹窗
						if (that.about_center[0].fr_sos_notice != '') {
							uni.showModal({
								title: '特别说明',
								content: that.about_center[0].fr_sos_notice,
								success: function(res) {
									if (res.confirm) {
										console.log('用户点击确定');
									} else if (res.cancel) {
										console.log('用户点击取消');
									}
								}
							});
						} else {
							console.log('设置紧急独立公告')
						};
					}
				});
			},

			// 引导添加到我的小程序
			fristNotice: function() {
				var that = this;
				var isFrisDotice = uni.getStorageSync('xsUserInfo_key');
				if (!isFrisDotice) {
					that.is_frist = true;
					setTimeout(function() {
						that.is_frist = false;
					}, 4000)
				} else {
					that.is_frist = false;
				}
			},
			// 轮播图获取
			lunbo: function() {
				var that = this;
				if (that.about_center[0].index_shuffling != '') {
					that.is_data_rotation = true;
					uni.request({
						url: API + '/wp-json/wp/v2/posts?include=' + that.about_center[0].index_shuffling,
						success: (res) => {
							that.rotation = res.data;
						}
					});
				} else {
					that.is_data_rotation = false;
					console.log('您没有配置首页轮播图')
				}

			},


			// 轮播图dot参数
			change(e) {
				this.current = e.detail.current
			},

			// 轮播图点击事件
			openSwipre: function(e) {
				var id = e;
				console.log('文章id是：', id)
				uni.navigateTo({
					url: '../data/data?id=' + id,
				})
			},

			// 金刚区跳转
			xs_default: function(index) {
				var that = this;
				console.log(that.vajra[index])
				if (that.vajra[index].type == 'xs_default') {
					uni.navigateTo({
						url: that.vajra[index].route
					})
				} else if (that.vajra[index].type == 'xs_tap') {
					uni.switchTab({
						url: that.vajra[index].route
					})
				}
			},

			// 滑动区域点击
			scrlistTap: function(e) {
				var that = this;
				var sve = e;
				uni.navigateTo({
					url: '../weblist/weblist?aul=' + sve
				})
			},

			// 菜单点击
			menuTap: function(index) {
				var tapIndex = index;
				var that = this;
				that.active = tapIndex;
				that.page = 1;
				that.newPost = [];
				that.listPosts();
			},

			// 菜单数据获取
			listPosts: function() {
				var that = this;

				// 获取顶部分类
				uni.request({
					url: API + '/wp-json/wp/v2/categories?per_page=100&include=' + that.about_center[0]
						.index_top,
					success: (res) => {
						that.menusLsit = res.data;
						var jlis = that.menusLsit.unshift({
							name: '最新文章'
						})
					}
				});

				// 判断是否是第几个选项卡
				if (that.active === 0) {
					console.log()
					if (that.about_center[0].categ_filter != '') {
						uni.request({
							url: API + '/wp-json/wp/v2/posts?page=' + that.page + '&categories_exclude=' + that
								.about_center[0].categ_filter,
							success: (res) => {
								if (!res.data.length) {
									that.no_list_data = true;
								} else {
									that.no_list_data = false;
									that.newPost = that.newPost.concat(res.data);
								}
							}
						});
					} else {
						uni.request({
							url: API + '/wp-json/wp/v2/posts?page=' + that.page + '&categories_exclude=0',
							success: (res) => {
								if (!res.data.length) {
									that.no_list_data = true;
								} else {
									that.no_list_data = false;
									that.newPost = that.newPost.concat(res.data);
								}
							}
						});
					}
				} else {
					uni.request({
						url: API + '/wp-json/wp/v2/posts?categories=' + that.menusLsit[that.active].id +
							'&page=' + that.page,
						success: (res) => {
							if (!res.data.length) {
								that.no_list_data = true;
							} else {
								that.no_list_data = false;
								that.newPost = that.newPost.concat(res.data);
							}
						}
					});
				}

			},

			// 插屏广告数据
			getWxchaAd: function() {
				var that = this;

				//#ifdef MP-WEIXIN	
				if (that.about_center[0].wx_chaping != '') {

					// 在页面onLoad回调事件中创建插屏广告实例
					if (wx.createInterstitialAd) {
						interstitialAd = wx.createInterstitialAd({
							adUnitId: that.about_center[0].wx_chaping
						})
						interstitialAd.onLoad(() => {})
						interstitialAd.onError((err) => {})
						interstitialAd.onClose(() => {})
					}

					// 在适合的场景显示插屏广告
					if (interstitialAd) {
						interstitialAd.show().catch((err) => {
							console.error(err)
						})
					}
				} else {
					console.log('没有流量主')
				}
				//#endif	

				//#ifdef MP-QQ
				console.log(that.about_center[0].qq_chaping)
				if (that.about_center[0].qq_chaping != '') {
					let InterstitialAd = qq.createInterstitialAd({
						adUnitId: that.about_center[0].qq_chaping
					});
					InterstitialAd.load().catch((err) => {
						console.error('load', err)
					})
					InterstitialAd.onLoad(() => {
						console.log('onLoad event emit')
					})
					InterstitialAd.onClose(() => {
						console.log('close event emit')
					})
					InterstitialAd.onError((e) => {
						console.log('error', e)
					})
					// 在适合的场景显示插屏广告
					if (interstitialAd) {
						/* 建议放在需要展示插屏广告的时机执行 */
						InterstitialAd.show().catch((err) => {
							console.error('show', err)
						})
					}
				} else {
					console.log('没有流量主')
				}

				//#endif
			},

			// 焦点点击
			wxMoreTap: function() {
				uni.navigateTo({
					url: '../wxmore/wxmore'
				})
			},

			// 文章列表点击
			newPostTap: function(e) {
				var posId = e;
				console.log(posId)
				uni.navigateTo({
					url: '../data/data?id=' + posId,

				})
			},

			// 海报分享点击
			posterTap: function() {
				var that = this;
				that.is_popup = true;
			},

			// 复制客服微信
			popupCustomer: function() {
				var that = this;
				//#ifdef MP-WEIXIN
				uni.setClipboardData({
					data: that.about_center[0].customer_wx,
					success: function() {
						console.log('success');
					}
				});
				//#endif 

				//#ifdef MP-QQ
				uni.setClipboardData({
					data: that.about_center[0].customer_qq,
					success: function() {
						console.log('success');
					}
				});
				//#endif
			},

			// 保存在相册
			popupSever: function() {
				var that = this;
				
				//#ifdef MP-WEIXIN
				uni.previewImage({
					current: 1,
					urls: [that.about_center[0].posterdata],
					longPressActions: {
						itemList: ['发送给朋友', '保存图片', '收藏'],
						success: function(data) {
							console.log('选中了第' + (data.tapIndex + 1) + '个按钮,第' + (data.index + 1) + '张图片');
						},
						fail: function(err) {
							console.log(err.errMsg);
						}
					}
				});
				//#endif
				
				//#ifdef MP-QQ
				uni.previewImage({
					current: 1,
					urls: [that.about_center[0].qq_posterdata_copy],
					longPressActions: {
						itemList: ['发送给朋友', '保存图片', '收藏'],
						success: function(data) {
							console.log('选中了第' + (data.tapIndex + 1) + '个按钮,第' + (data.index + 1) + '张图片');
						},
						fail: function(err) {
							console.log(err.errMsg);
						}
					}
				});
				//#endif
			},

			// 关闭遮照层
			popupExit: function() {
				var that = this;
				that.is_popup = false;
			},

			// 最新文章的查看更多点
			categoryNew: function() {
				uni.switchTab({
					url: "../category/category"
				})
			}

		}

	}
</script>

<style>
	/* 首页分类样式1 */
	.muen-tow-list-one {
		margin: 30upx;
	}

	.muen-tow-view {
		border: 20upx 20upx 0upx 0upx;
		overflow: hidden;
		padding-bottom: 30upx;
	}

	.muen-tow-list {
		overflow: hidden;
		border-radius: 20upx;
	}

	/* 首页分类样式2 */
	.list-one-time-n {
		color: #D3D3D3;
		font-size: 10px;
		height: 16upx;
		line-height: 16upx;
		margin-left: 8upx;
	}



	.list-one-time-l {
		height: 18upx;
		width: 30upx;
		border-radius: 50upx;
		background: linear-gradient(90deg, rgba(67, 130, 235, 1) 0%, rgba(6, 189, 254, 1) 100%);
	}

	.list-one-time-y {
		height: 18upx;
		width: 30upx;
		border-radius: 50upx;
		background: linear-gradient(90deg, rgba(12, 185, 162, 1) 0%, rgba(15, 236, 210, 1) 100%);
	}

	.list-one-time {
		display: flex;
		margin-top: 18upx;
		margin-left: 18upx;

	}

	.list-one-qu {
		display: flex;
		align-items: center;
		justify-content: flex-end;
	}

	.list-ona-abstract {
		color: #ADADAD;
		font-size: 24upx;
		overflow: hidden;
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-line-clamp: 1;
		-webkit-box-orient: vertical;
		margin-top: 2upx;
	}

	.list-one-title {
		font-size: 28upx;
		border-radius: 0upx 0upx 20upx 20upx;
		margin-top: -10upx;
		padding: 10upx 0upx;
	}

	.list-one-title-two {
		font-size: 30upx;
		overflow: hidden;
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-line-clamp: 2;
		-webkit-box-orient: vertical;
	}

	.list-one-data-w {
		margin: 28upx;
	}

	.list-one-img {
		height: 280upx;
		border-radius: 20upx;
		overflow: hidden;
	}

	.list-one-w {
		/* box-shadow: 0px 6px 9px rgba(0, 0, 0, 0.16); */
		border-radius: 20upx;
		margin: 40upx 0upx;
		overflow: hidden;
		background-color: #FFFFFF;
	}


	/* 弹窗 */
	.popup-close {
		height: 80upx;
		width: 80upx;
		overflow: hidden;
		margin: 30upx auto;
	}

	.popup-fiend {
		width: 40%;
		height: 60upx;
		border-radius: 200upx;
		background-color: #2f75f5;
		color: #FFFFFF;
		font-size: 20upx;
		line-height: 60upx;
	}

	.popup-phothop {
		width: 40%;
		height: 60upx;
		border-radius: 200upx;
		background-color: #FFFFFF;
		font-size: 20upx;
		line-height: 60upx;
	}

	.popup-btn {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-top: 40upx;
	}

	.popup-img {
		height: 800upx;
		width: 550upx;
		overflow: hidden;
		border-radius: 14upx;
	}

	.popup-about {
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
	}

	.popup-view {
		position: fixed;
		height: 100%;
		width: 100%;
		background-color: rgba(0, 0, 0, 0.72);
		z-index: 99;
		top: 0;
		left: 0;

	}

	/* 引导添加我的小程序 */
	.frist-notic {
		background-color: #1190ed;
		position: fixed;
		top: 120upx;
		right: 48upx;
		width: 360upx;
		padding: 20upx;
		z-index: 99;
		border-radius: 10upx;
		color: #faffff;
		text-align: center;
		font-size: 20upx;
		box-shadow: #57595f 0upx 2upx 10upx;
	}

	/* 列表没有数据 */
	.no-list-data {
		text-align: center;
		margin: 30rpx 0px;
		color: #e6e6e6;
		font-size: 24upx;
	}

	/* 内容数据 */
	.menu-scroll-list-describe {
		color: #ADADAD;
		font-size: 20upx;
		overflow: hidden;
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-line-clamp: 1;
		-webkit-box-orient: vertical;
	}

	.list-li-tag {
		color: #0BBDA6;
		font-size: 20upx;
	}

	.menu-scroll-list-h {
		font-size: 26upx;
		overflow: hidden;
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-line-clamp: 2;
		-webkit-box-orient: vertical;
	}

	.menu-scroll-list-right {
		margin-left: 32upx;
		width: 420upx;
		display: flex;
		justify-content: space-between;
		flex-flow: column;
	}

	.menu-scroll-list-img {
		width: 360upx;
		height: 180upx;
		border-radius: 16upx;
		overflow: hidden;
	}

	.menu-scroll-list {
		display: flex;
		padding: 30upx;
		background-color: #FFFFFF;
		margin: 30upx 0upx;
		border-radius: 20upx;
	}

	.postlist-bg {
		border-radius: 20upx;
		overflow: hidden;
		margin: 0upx 30upx;
	}

	/* 区域滑动 */
	.scroll-view-item-describe {
		color: #ADADAD;
		font-size: 24upx;
		margin: 6upx 30upx 0upx 30upx;
		overflow: hidden;
	}

	.scroll-img {
		height: 30upx;
		width: 30upx;
	}

	.scroll-view-item-h-title {
		font-size: 24upx;
		margin-left: 18upx;
		overflow: hidden;
		text-overflow: ellipsis;
		-webkit-line-clamp: 2;
		-webkit-box-orient: vertical;
		margin-top: 12upx;
	}

	.scroll-view-item-h-describe {
		font-size: 22upx;
		color: #807C7C;
		margin-left: 18upx;
		overflow: hidden;
		text-overflow: ellipsis;
		-webkit-line-clamp: 2;
		-webkit-box-orient: vertical;
		margin-top: 12upx;
		margin-bottom: 18upx;
	}

	.scroll-view-item-h-img {
		width: 100%;
		height: 180upx;
		overflow: hidden;
	}

	.scroll-view-item-list-star {
		width: 0upx;
		border-radius: 20upx;
		margin-right: 10upx;
		margin-left: 20upx;
		display: inline-block;
		overflow: hidden;
	}

	.scroll-view-item-list {
		width: 420upx;
		/* height: 240upx; */
		border-radius: 20upx;
		display: inline-block;
		overflow: hidden;
		background-color: #FFFFFF;
		margin-left: 30upx;
	}

	.scroll-view-item-list:last-child {
		margin-right: 30upx;
	}


	.swiper-list {
		white-space: nowrap;
		width: 100%;
	}

	/* 标题 */
	.titel-vi-right {
		color: #989898;
		height: 80upx;
		line-height: 80upx;
		font-size: 20upx;
	}

	.titel-vi-font {
		font-size: 30upx;
		margin-left: 24upx;
	}

	.titel-vi-img {
		width: 36upx;
		height: 36upx;
	}

	.titel-vi {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin: 28upx 30upx 0upx 30upx;
	}

	.titel-vi-q {
		display: flex;
		justify-content: flex-start;
		align-items: center;
	}

	/* 顶部菜单 */
	.menu_scroll_top_one {
		position: fixed;
		top: -1upx;
		left: 0;
		/* background-color: #F7F7F7; */
		width: 100%;
		z-index: 90;
		padding-top: 100upx;
	}

	.menu_scroll_top_tow {
		position: fixed;
		top: -1upx;
		left: 0;
		background-color: #ffffff;
		width: 100%;
		z-index: 90;
		padding-top: 100upx;
		border-radius: 0upx 0upx 30upx 30upx;
		box-shadow: 0 8px 16px 0 rgba(0, 0, 0, 0.10);
	}

	.menu_list_img_ti {
		height: 30upx;
		width: 30upx;
		margin-right: 10upx;
		float: left;
		overflow: hidden;
	}

	.active_menu_list_img_ti {
		height: 40upx;
		width: 40upx;
		margin-right: 10upx;
		float: left;
	}

	.active_menu_scroll_leat_font {
		font-size: 26upx;
		padding: 16upx 30upx;
		justify-content: center;
		align-items: center;
		flex-shrink: 0;
		width: auto;
		display: inline-block;
		background-color: #007AFF;
		border-radius: 12upx;
		color: #FFFFFF;
		margin-right: 20upx;
	}

	.menu_scroll_leat_font {
		color: #ADADAD;
		font-size: 26upx;
		padding: 16upx 30upx;
		justify-content: center;
		align-items: center;
		filter: grayscale(1);
		flex-shrink: 0;
		width: auto;
		display: inline-block;
		margin-right: 20upx;
		border-radius: 12upx;
	}

	.menu-scroll-right {
		width: 30upx;
		height: 6upx;
	}

	.menu-scroll-leat {
		align-items: flex-end;
		white-space: nowrap;
		background-color: rgba(255, 255, 255, 0.4);
		border-radius: 20upx;
		padding: 20upx;
	}

	.menu-scroll {
		display: flex;
		justify-content: space-between;
		padding: 0upx 30upx 10upx 30upx;
		justify-content: flex-start;
	}

	/* 金刚区域 */
	.Vajra-list-font {
		color: #807C7C;
		font-size: 24upx;
	}

	.Vajra-list-img {
		height: 90upx;
		width: 90upx;
		overflow: hidden;
		margin: auto;
	}

	.Vajra-list {
		text-align: center;
		/* flex-shrink: 0; */
		width: 23%;
		margin: auto;
	}

	.Vajra {
		margin: 20upx;
		display: flex;
		justify-content: space-between;
		align-items: center;
		/* overflow-x: auto; */
	}

	.Vajra-bg {
		background-color: #FFFFFF;
		border-radius: 20upx;
		margin: 30upx;
		padding: 1upx;
	}

	/* 海报 */
	.poster {
		display: flex;
		/* height: 120upx; */
		margin: 0upx 48upx;
		border-top: #F5F5F5 1px solid;
		padding-top: 20upx;
		padding-bottom: 14upx;
		align-items: center;
		justify-content: space-between;
	}

	.poster-imgs {
		height: 100upx;
		width: 140upx;
	}

	.poster-text {
		font-size: 24upx;
		color: #807C7C;
	}

	.poster-btn {
		height: 50upx;
		background-color: #007AFF;
		border-radius: 200upx;
		font-size: 20upx;
		width: 140upx;
		color: #FFFFFF;
		flex-shrink: 0;
		margin-right: 0upx;
		margin-left: 0upx;
	}

	/* 公告 */
	.notice-flex {
		display: flex;
		height: 60upx;
		margin: 0upx 30upx;
		border-top: #F5F5F5 1px solid;
		background-color: #FFFFFF;
		padding: 20upx 48upx 14upx 48upx;
		border-radius: 20upx;
	}

	.notice-img {
		height: 30upx;
		width: 140upx;
		flex-fhrink: 0;
		margin-top: 10upx;
	}

	.notice-fr-title {
		width: calc(100% - 140upx);
	}

	/* 轮播图 */
	.swiper-item {
		border-radius: 16upx;
		overflow: hidden;
		height: 320upx;
	}

	.swiper-box {
		height: 320upx;
		border-radius: 16upx;
		overflow: hidden;
	}

	.image {
		width: 750rpx;
	}

	/* 全局 */
	.content-all {
		margin: -180upx 30upx 30upx 30upx;
		z-index: 30;
	}

	.fengrui-img {
		width: 100%;
		height: 100%;
	}


	.index-ov {
		overflow-x: hidden;
	}

	.tar-w {
		background-color: #FFFFFF;
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		z-index: 99999999999;
	}

	page {
		background-color: #F7F7F7;
		overflow-x: hidden;
	}

	/* 暗黑模式下应用的样式 */
	@media (prefers-color-scheme: dark) {
		page {
			background: #161616;
		}

		.Vajra-bg {
			background: #202020;
		}

		.scroll-view-item-list {
			background: #202020;
		}

		.menu-scroll-list {
			background: #202020;
		}

		.scroll-view-item-list {
			border: 1px #191919 solid;
		}

		.notice-flex {
			border-top: #484848 1px solid;
		}

		.menu_scroll_top {
			background: #161616;
		}

		.menu_scroll_top_tow {
			background-color: #202020;
		}

		.notice-flex {
			background: #202020;
		}

		.list-one-w {
			border: 1px #191919 solid;
			background: #202020;
		}

		.poster {
			border-top: #000000 1px solid;
		}
	}
</style>
