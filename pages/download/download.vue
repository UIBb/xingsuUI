<template>
	<view class="">
		<view>
			<!-- 标签-->
			<view class="swiper-list">
				<scroll-view class="" scroll-x="true">
					<view :class="active == index ? 'active-label-list':'label-list' " v-for="(menu,index) in menusLsit"
						:key="index" @tap="menuTap(index)">
						{{ menu.name }}
					</view>
				</scroll-view>
			</view>

			<!-- #ifdef MP-WEIXIN -->
			<!-- 流量主广告或者图片 -->
			<view class="me-ads">
				<block v-if="about_center.length>0">
					<view class="">
						<ad :unit-id="about_center[0].wx_gezi" ad-type="grid" grid-opacity="0.8" grid-count="5"
							ad-theme="white"></ad>
					</view>
				</block>
			</view>
			<!-- #endif -->

			<!-- 资源列表 -->
			<block v-for="(relist ,index) in reclist" :key="index">
				<view class="list-view-bg" v-if=" relist.account_url != '' || relist.down_url != '' ">
					<view class="list-view-img" @tap="newPostTap(relist.id)">
						<image :src="relist.thumbnailurl" class="fengrui-img" mode="aspectFill"></image>
					</view>
					<!-- 资源列表 第一个if判断阅读激励视频方式 第二个判断是否全栈开启下载资源下载 -->
					<view class="list-view" v-if="qq_down_shop == '0' ">
						<view class="list-li-toew">
							<view class="list-li-left">
								<view class="list-li-left-h">
									{{ relist.down_title }}
								</view>
								<view class="list-li-tag">
									成功复制链接后请到浏览器下载
								</view>
							</view>
						</view>
						<view class="list-li-toew list-li-toew-ma">
							<view class="list-icon-list" v-if="relist.xsgoshop !='' " @tap="tapShop(relist.xsgoshop)">
								<view class="list-icon-list-img">
									<image class="fengrui-img" src="../../static/data/renking-8.svg" mode="aspectFit">
									</image>
								</view>
								<view class="list-icon-list-img-title">
									商店
								</view>
							</view>
							<view class="list-icon-list" v-if=" relist.down_url != '' " @tap="tapDown(relist.down_url)">
								<view class="list-icon-list-img">
									<image class="fengrui-img" src="../../static/data/renking-1.svg" mode="aspectFit">
									</image>
								</view>
								<view class="list-icon-list-img-title">
									网盘
								</view>
							</view>

							<!-- #ifdef MP-WEIXIN -->
							<view class="list-icon-list" v-if=" relist.account_url != '' "
								@tap="tapWx(relist.account_url)">
								<view class="list-icon-list-img">
									<image class="fengrui-img" src="../../static/data/renking-9.svg" mode="aspectFit">
									</image>
								</view>
								<view class="list-icon-list-img-title">
									软文
								</view>
							</view>
							<!-- #endif -->

							<view class="list-icon-list" @tap="newPostTap(relist.id)">
								<view class="list-icon-list-img">
									<image class="fengrui-img" src="../../static/data/renking-10.svg" mode="aspectFit">
									</image>
								</view>
								<view class="list-icon-list-img-title">
									文章
								</view>
							</view>
						</view>
					</view>
				</view>
			</block>
		</view>
	</view>
</template>

<script>
	// 引入资源文件
	import {
		API
	} from '@/components/api.js';
	let rewardedVideoAd = null;
	export default {
		data() {
			return {
				// 下载列表默认封面图片
				//注图片来自：https://www.iconfont.cn/illustrations/detail?spm=a313x.7781069.1998910419.d9df05512&cid=26334
				relist_img: '../../static/down/down.svg',
				statement: '部分资源来自第三方',

				// 资源列表
				reclist: [],
				about_center: [],
				dnurl: '',
				special: [],
				page: 1,
				qq_down_shop:'0',
				id_date_show: false,
				//分类菜单
				active: 1,
				menusLsit: [],
				newPost: [],
			}
		},
		onLoad() {

			// 进来提示
			uni.showToast({
				icon: 'none',
				title: "页面正在加载中，请稍等片刻"
			})
			// 发起请求
			this.getdown();

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

		// 监听触底----上啦刷新
		onReachBottom() {
			this.page = this.page + 1;
			this.DataLoing();
		},

		// 分享好友配置
		onShareAppMessage(res) {
			var that = this;
			if (res.from === 'button') { // 来自页面内分享按钮
				console.log(res.target)
			}
			return {
				title: that.about_center[0].share_title,
				imageUrl: that.about_center[0].share_title_url,
				path: '/pages/download/download'
			}
		},
		methods: {
			// 发起请求
			getdown: function() {
				var that = this;
				// 请求流量主数据
				uni.request({
					url: API + '/wp-json/wp/v2/fengrui',
					success: (res) => {
						that.about_center = res.data;
						
						//#ifdef MP-QQ
						that.qq_down_shop = res.data[0].xs_qq_off_down;
						//#endif
						
						// 初始化激励视频广告
						that.CreateAd();

						// 首页顶部菜单
						this.menuTap(0);
					}
				});
			},

			// 菜单点击
			menuTap: function(index) {
				var tapIndex = index;
				var that = this;
				that.active = tapIndex;
				that.page = 1;
				that.reclist = [];
				that.listPosts();
			},
			// 菜单数据获取
			listPosts: function() {
				var that = this;

				// 获取顶部分类
				uni.request({
					url: API + '/wp-json/wp/v2/categories?per_page=100&include=' + that.about_center[0]
						.rec_top_cate,
					success: (res) => {
						that.menusLsit = res.data;
						var jlis = that.menusLsit.unshift({
							name: '全部'
						})
						that.DataLoing();
					}
				});



			},

			// 数据加载
			DataLoing: function() {
				var that = this;
				// 判断是否是第几个选项卡
				if (that.active === 0) {
					// 请求资源数据
					uni.request({
						url: API + '/wp-json/wp/v2/posts?page=' + that.page,
						success: (res) => {
							if (res.statusCode == 200) {
								that.reclist = that.reclist.concat(res.data);
							} else {
								console.log('m没有数据了')
							}
						}
					});
				} else {
					// 请求资源数据
					uni.request({
						url: API + '/wp-json/wp/v2/posts?categories=' + that.menusLsit[that.active].id +
							'&page=' + that.page,
						success: (res) => {
							if (res.statusCode == 200) {
								that.reclist = that.reclist.concat(res.data);
							} else {
								console.log('m没有数据了')
							}
						}
					});

				}
			},

			//初始化激励视频广告组件
			CreateAd: function() {
				var that = this;
				if (that.about_center[0].wx_jili_video != '') {
					if (wx.createRewardedVideoAd) {
						that.videoAd = wx.createRewardedVideoAd({
							adUnitId: that.about_center[0].wx_jili_video
						})
						that.videoAd.onLoad(() => {})
						that.videoAd.onError((err) => {
							uni.showToast({
								icon: 'none',
								title: "观看失败,请稍后重试1"
							})
						})
						that.videoAd.onClose((res) => {
							if (res && res.isEnded) {
								uni.setClipboardData({
									data: that.dnurl,
									success: function() {
										console.log('success');
									}
								});
							} else {
								uni.showToast({
									icon: 'none',
									title: "中途关闭广告"
								})
							}
						})
					}
				} else {
					console.log('好像没有广告配置')
				}
			},

			// 点击触发激励视频
			// #ifdef MP-WEIXIN
			getVideoAd: function() {
				let that = this;
				// 用户触发广告后，显示激励视频广告
				if (that.videoAd) {
					that.videoAd.show().catch(() => {
						// 失败重试
						that.videoAd.load()
							.then(() => that.videoAd.show())
							.catch(err => {
								console.log('激励视频 广告显示失败')
								uni.showToast({
									icon: 'none',
									title: "观看失败,请稍后重试"
								})
							})
					})
				}
			},
			// #endif

			// #ifdef MP-QQ
			getVideoAd: function() {
				let that = this;
				// 检测是否有激励视频id
				if (that.about_center[0].wx_jili_video != '') {
					let videoAd = qq.createRewardedVideoAd({
						adUnitId: that.about_center[0].qq_jili_video
					});
					videoAd.onError(function(res) {
						console.log('videoAd onError', res)
					});
					videoAd.onLoad(function(res) {
						console.log('videoAd onLoad', res)
					});
					videoAd.onClose(function(res) {
						console.log('videoAd onClose', res)
						if (res.isEnded === true) {
							that.isShowAd = true;
						} else {
							that.isShowAd = false;
						}
					});
					videoAd.load().then(() => {
						console.log('激励视频加载成功');
						videoAd.show().then(() => {
							that.isShowAd = false;
							console.log('激励视频 广告显示成功')
						}).catch(err => {
							console.log('激励视频 广告显示失败')
							that.isShowAd = false;
							uni.showToast({
								icon: 'none',
								title: "观看失败,请稍后重试"
							})
						})
					}).catch(err => {
						console.log('激励视频加载失败');
					})

				} else {
					that.isShowAd = true;
					console.log('没有QQ流量主')
				}
			},
			// #endif

			// 访问微信文章
			tapWx: function(wx) {
				var that = this;
				var sve = wx;
				uni.navigateTo({
					url: '../weblist/weblist?aul=' + sve
				})
			},

			// 打开文章
			newPostTap: function(e) {
				var posId = e;
				console.log(posId)
				uni.navigateTo({
					url: '../data/data?id=' + posId,

				})
			},

			// 复制下载地址
			tapDown: function(dn) {
				var that = this;
				that.dnurl = dn;
				//#ifdef MP-WEIXIN
				if (that.about_center[0].wx_jili_video != '') {
					if (that.reclist.if_rec_down_video != '1') {
						that.jili_down();
					} else {
						that.no_jili_down();
					}
				} else {
					that.no_jili_down();
				}
				//#endif

				//#ifdef MP-QQ
				if (that.about_center[0].qq_jili_video != '') {
					if (that.reclist.if_rec_down_video != '1') {
						that.jili_down();
					} else {
						that.no_jili_down();
					}
				} else {
					that.no_jili_down();
				}
				//#endif
			},

			// 激励视频下载
			jili_down: function() {
				var that = this;
				uni.showModal({
					title: '下载提示',
					content: '需要观看视频广告后自动复制',
					showCancel: true,
					cancelText: '取消',
					confirmText: '观看复制',
					success: res => {
						if (res.confirm) {
							console.log('用户点击确定' + '要看视频');
							that.getVideoAd();
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					},
					complete: () => {}
				});
			},

			// 直接下载
			no_jili_down: function() {
				var that = this;
				uni.showModal({
					title: '自动复制',
					content: '链接自动复制成功后到浏览器下载',
					showCancel: true,
					cancelText: '取消',
					confirmText: '复制',
					success: res => {
						if (res.confirm) {
							console.log('用户点击确定');
							uni.setClipboardData({
								data: that.dnurl,
								success: function() {
									console.log('success');
								}
							});
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					},
					complete: () => {}
				});
			},

			// 打开小商店详情
			tapShop: function(e) {
				var that = this;
				//#ifdef MP-WEIXIN
				uni.navigateToMiniProgram({
					appId: that.about_center[0].xs_shop_appid,
					path: 'plugin-private://wx34345ae5855f892d/pages/productDetail/productDetail?productId=' +
						e,
					success(res) {
						// 打开成功
					}
				})
				//#endif

				//#ifdef MP-QQ
				uni.showModal({
					title: '多端说明',
					content: '该操作不支持QQ端！！！',
					success: function(res) {
						if (res.confirm) {
							console.log('用户点击确定');
							that.getVideoAd();
						} else if (res.cancel) {
							console.log('用户点击取消');
							that.tarBlack();
						}
					}
				});
				//#endif
			},
		}
	}
</script>

<style>
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


	/* 标签 */
	.swiper-list {
		margin: 20upx 0upx 30upx 0upx;
		white-space: nowrap;
		padding-right: 20upx;
	}

	.label-list {
		height: 90upx;
		padding: 10upx 40upx;
		background-color: #efefef;
		border-radius: 20upx;
		margin-left: 30upx;
		color: #cacaca;
		font-size: 28upx;
		flex-shrink: 0;
		line-height: 90upx;
		text-align: center;
		display: inline-block;
	}

	.active-label-list {
		height: 90upx;
		padding: 10upx 40upx;
		background: linear-gradient(90deg, rgba(67, 130, 235, 1) 0%, rgba(6, 189, 254, 1) 100%);
		border-radius: 20upx;
		margin-left: 20upx;
		color: #FFFFFF;
		font-size: 28upx;
		flex-shrink: 0;
		line-height: 90upx;
		text-align: center;
		display: inline-block;
	}

	/* 没有数据时候 */
	.no-text {
		color: #ADADAD;
		margin-top: 40upx;
		font-size: 30upx;
	}

	.no-img {
		border-radius: 20rpx;
		overflow: hidden;
		height: 700upx;
		width: 500upx;
	}

	.no-datalist {
		position: absolute;
		top: 40%;
		left: 50%;
		transform: translate(-50%, -50%);
	}

	/* 资源 */
	.list-li-left-describe {
		margin: 30upx 0upx 10upx 0upx;
		font-size: 20upx;
		display: flex;
		justify-content: space-between;
		align-items: center;
	}

	.list-li-tag {
		color: #D5D5D5;
		font-size: 20upx;
		overflow: hidden;
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-line-clamp: 2;
		-webkit-box-orient: vertical;
		margin-top: 20upx;
	}

	.list-img {
		height: 100upx;
		width: 120upx;
		border-radius: 14upx;
		overflow: hidden;
		flex-shrink: 0;
	}

	.list-view {
		background-color: #151515a8;
		border-radius: 20upx;
		bottom: 40upx;
		left: 5%;
		width: 90%;
		position: absolute;
		box-shadow: 0 8px 16px 0 rgba(0, 0, 0, 0.10);
	}

	.list-li {
		display: flex;
		align-items: center;
		margin: 20upx 0upx;
	}

	.list-li-left-describe {
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-line-clamp: 1;
		-webkit-box-orient: vertical;
		color: #D5D5D5;
		font-size: 20upx;
		display: flex;
		justify-content: space-between;
	}

	.list-li-left-h {
		font-size: 36upx;
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-line-clamp: 2;
		-webkit-box-orient: vertical;
		color: #FFFFFF;
	}

	.list-li-left {
		margin-left: 32upx;
		flex-grow: 1;
		display: flex;
		flex-flow: column;
		justify-content: space-between;
	}

	.list-img {
		height: 100upx;
		width: 120upx;
		border-radius: 14upx;
		overflow: hidden;
		flex-shrink: 0;
	}

	.list-li {
		display: flex;
		margin: 20upx 48upx;
		align-items: center;
		border-radius: 20upx;
	}

	.list-li-toew {
		display: flex;
		margin: 30upx 0upx;
		align-items: center;
		border-radius: 20upx;
		justify-content: space-around;
	}

	.list-icon-list {
		text-align: center;
	}

	.list-icon-list-img {
		height: 50upx;
		width: 50upx;
		overflow: hidden;

	}

	.list-icon-list-img-title {
		font-size: 22upx;
		color: #b1b1b1;
		margin-top: 12upx;
	}

	.list-li-toew-ma {
		margin: 30upx 0upx 20upx 0upx;
	}

	.list-view-bg {
		background-color: #FFFFFF;
		border-radius: 20upx;
		overflow: hidden;
		position: relative;

		margin: 50upx 30upx;
	}

	.list-view-img {
		height: 620upx;
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

	.district-money {
		background: linear-gradient(90deg, rgba(12, 185, 162, 1) 0%, rgba(15, 236, 210, 1) 100%);
		width: 140upx;
		height: 50upx;
		line-height: 50upx;
		border-radius: 200upx;
		text-align: center;
		line-height: 50upx;
		color: #FFFFFF;
		font-size: 24upx;
		margin-left: 30upx;
		box-shadow: #0bbda6 0upx 4upx 12upx;
	}

	/* 顶部图片 */
	.top-right-top {
		width: 100%;
		height: 160upx;
		border-radius: 20upx;
		width: 316upx;
		overflow: hidden;
	}

	.top-statement {
		color: #c0c0c0;
		font-size: 22upx;
	}

	.top-img {
		width: 100%;
		height: 340upx;
		width: 350upx;
		border-radius: 20upx;
		overflow: hidden;
	}

	.top-view {
		margin: 30upx;
		border-radius: 20upx;
		overflow: hidden;
		display: flex;
		justify-content: space-between;
		align-items: center;
	}

	.me-ads {
		border-radius: 18upx;
		margin: 48upx 0upx;
		overflow: hidden;
		margin: 30upx;
	}

	button:after {
		border: 0px solid rgba(0, 0, 0, .2);

	}

	.fengrui-img {
		width: 100%;
		height: 100%;
	}

	page {
		background-color: #F7F7F7;
	}

	/* 暗黑模式下应用的样式 */
	@media (prefers-color-scheme: dark) {
		page {
			background: #161616;
		}

		.list-view {
			background: #202020;
		}
	}
</style>
