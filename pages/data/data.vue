<template>
	<view class="index-ov">
		<view>
			<view class="back-img">
				<!-- 资源列表 第一个if判断阅读激励视频方式 第二个判断是否全栈开启下载资源下载 -->
				<block v-if="qq_down_shop == '0'">
					<view class="list-view" v-if=" detailData.account_url != '' || detailData.down_url != '' ">
						<view class="list-li-toew">
							<view class="list-img">
								<image class="fengrui-img" src="../../static/down/down.svg" mode="aspectFill"></image>
							</view>
							<view class="list-li-left">
								<view class="list-li-left-h">
									{{ detailData.down_title }}
								</view>
								<view class="list-li-tag">
									成功复制链接后请到浏览器下载
								</view>
							</view>
						</view>
						<view class="list-li-toew list-li-toew-ma">
							<view class="list-icon-list" @tap="tapShop(detailData.xsgoshop)">
								<view class="list-icon-list-img">
									<image class="fengrui-img" src="../../static/data/renking-8.svg" mode="aspectFit">
									</image>
								</view>
								<view class="list-icon-list-img-title">
									商店
								</view>
							</view>
							<view class="list-icon-list" v-if=" detailData.down_url != '' "
								@tap="tapDown(detailData.down_url)">
								<view class="list-icon-list-img">
									<image class="fengrui-img" src="../../static/data/renking-1.svg" mode="aspectFit">
									</image>
								</view>
								<view class="list-icon-list-img-title">
									网盘
								</view>
							</view>
							<view class="list-icon-list" @tap="tapMoney()">
								<view class="list-icon-list-img">
									<image class="fengrui-img" src="../../static/data/renking-7.svg" mode="aspectFit">
									</image>
								</view>
								<view class="list-icon-list-img-title">
									奶茶
								</view>
							</view>
							<view class="list-icon-list" v-if=" detailData.account_url != '' "
								@tap="tapWx(detailData.account_url)">
								<view class="list-icon-list-img">
									<image class="fengrui-img" src="../../static/data/renking-9.svg" mode="aspectFit">
									</image>
								</view>
								<view class="list-icon-list-img-title">
									原文
								</view>
							</view>
							<!-- #ifdef MP-WEIXIN -->
							<button class="list-icon-list-btn" open-type="contact">
								<view class="list-icon-list-img">
									<image class="fengrui-img" src="../../static/my/customer.png" mode="aspectFit">
									</image>
								</view>
								<view class="list-icon-list-img-title">
									客服
								</view>
							</button>
							<!-- #endif -->
						</view>
					</view>
				</block>
			</view>
		</view>

		<!-- 标题 -->
		<view class="titel-h-w">
			<view class="titel-h">
				{{ posdCenterTitle }}
			</view>
		</view>

		<!-- 时间和装饰品 -->
		<view class="time-view">
			<view class="time-img ">
				<image class="fengrui-img" src="../../static/data/renking-8.svg" mode=""></image>
			</view>
			<view class="time-img time-le">
				<image class="fengrui-img" src="../../static/data/renking-1.svg" mode=""></image>
			</view>
			<view class="time-img time-le">
				<image class="fengrui-img" src="../../static/data/renking-7.svg" mode=""></image>
			</view>
			<view class="time-img time-le">
				<image class="fengrui-img" src="../../static/data/comment.svg" mode=""></image>
			</view>
			<view class="time-img time-le">
				<image class="fengrui-img" src="../../static/my/star.png" mode=""></image>
			</view>
			<view class="time-img time-le">
				<image class="fengrui-img" src="../../static/data/time.png" mode=""></image>
			</view>
			<view class="time-font">
				{{ detailData.date }}
			</view>
		</view>

		<!-- 关注微信公众号 -->
		<!-- #ifdef MP-WEIXIN -->
		<view class="account-view">
			<view class="account-flex">
				<view class="account-img">
					<image class="fengrui-img" :src="about_center[0].public_logo" mode=""></image>
				</view>
				<view class="account-aout">
					<view class="account-name">
						{{ about_center[0].public_name }}
					</view>
					<view class="account-describe">
						{{ about_center[0].public_describe }}
					</view>
				</view>
			</view>
			<view class="">
				<button class="account-btn" @tap="accountTap(about_center[0].public_follow)">关注</button>
			</view>
		</view>
		<!-- #endif -->

		<!-- 正文内容 -->
		<view class="data-view">
			<u-parse :content="detailData.content.rendered" :loading="loading" @preview="preview"
				@navigate="navigate" />
		</view>


		<!-- me-svg -->
		<view class="me-svg">
			<!-- #ifdef MP-WEIXIN -->
			<button class="me-sbg-btn" @tap="tapMoney()">
				<view class="Vajra-list-img">
					<image class="fengrui-img" src="../../static/data/renking-7.svg" mode="aspectFill"></image>
				</view>
				<view class="Vajra-list-font">
					赞助
				</view>
			</button>
			<!-- #endif -->
			<!-- #ifdef MP-QQ -->
			<button class="me-sbg-btn" @tap="tarBlack()">
				<view class="Vajra-list-img">
					<image class="fengrui-img" src="../../static/my/star.png" mode="aspectFill"></image>
				</view>
				<view class="Vajra-list-font">
					返回
				</view>
			</button>
			<!-- #endif -->
			<button class="me-sbg-btn" open-type="share">
				<view class="Vajra-list-img">
					<image class="fengrui-img" src="../../static/my/poster.png" mode="aspectFill"></image>
				</view>
				<view class="Vajra-list-font">
					转发
				</view>
			</button>
			<!-- #ifdef MP-WEIXIN -->
			<button class="me-sbg-btn" open-type="contact">
				<view class="Vajra-list-img">
					<image class="fengrui-img" src="../../static/my/customer.png" mode="aspectFill"></image>
				</view>
				<view class="Vajra-list-font">
					咨询客服
				</view>
			</button>
			<!-- #endif -->

			<!-- qq端调用添加好友 -->
			<!-- #ifdef MP-QQ -->
			<button class="me-sbg-btn" open-type="addFriend" @tap="addFr()" :open-id="opId[0].opIdCode"
				v-if="is_qq_openid">
				<view class="Vajra-list-img">
					<image class="fengrui-img" src="../../static/my/customer.png" mode="aspectFill"></image>
				</view>
				<view class="Vajra-list-font">
					咨询客服
				</view>
			</button>
			<!-- #endif -->
		</view>


		<!-- #ifdef MP-WEIXIN -->
		<view v-if="about_center.length>0" style="width: 94%;padding-left: 3%;margin-top: 50upx;">
			<ad :unit-id="about_center[0].wx_video" ad-type="video" ad-theme="white"></ad>
		</view>
		<!-- #endif -->

		<!-- #ifdef MP-QQ -->
		<view v-if="about_center.length>0" style="width: 94%;padding-left: 3%;margin-top: 50upx;">
			<ad :unit-id="about_center[0].qq_video" ad-type="video" ad-theme="white"></ad>
		</view>
		<!-- #endif -->

		<!-- 猜你想搜 -->
		<view class="titel-h-go">
			惺惺相惜
		</view>
		<!-- 列表 -->
		<block>
			<view class="list-li" v-for="(item ,index) in posTagsList" :key="index" @click="posTap(item.id)">
				<view class="list-img">
					<image class="fengrui-img" :src="item.thumbnailurl" mode="aspectFill"></image>
				</view>
				<view class="list-li-left">
					<view class="list-li-left-h">
						{{ item.title.rendered }}
					</view>
					<view class="list-li-left-describe">
						<view class="">推举阅读</view>
						<view>{{ item.date }}</view>
					</view>
				</view>
			</view>
			<view class="upda-view">

			</view>
		</block>

		<!-- 文章列表没有数据 -->
		<view class="no-list-data" v-if="no_list_data">
			--我的底线就到这里了--
		</view>

		<!-- 提问转发 -->
		<!-- #ifdef MP-WEIXIN -->
		<cover-view class="cover-pos-view" v-if="isShowAd">
			<cover-view class="cover-pos-que" @tap="questionsTap()">提交问题</cover-view>
			<cover-view class="cover-pos-fu" v-if=" detailData.account_url != '' || detailData.down_url != '' "
				@tap="enclosure()">附件</cover-view>
		</cover-view>
		<!-- #endif -->
	</view>

</template>

<script>
	// 引入域名和自定义php文件
	import {
		API
	} from '@/components/api.js';
	import {
		Getresources
	} from '@/components/api.js';
	import uParse from '@/components/gaoyia-parse/parse.vue'
	let rewardedVideoAd = null;
	export default {
		components: {
			uParse
		},
		data() {
			return {
				posdCenterTitle: '数据加载中....',
				detailData: [],
				posID: '',
				tarplay: false,
				tarpHaight: '44',
				BarHeight: this.BarHeight,
				TabHeight: this.TabHeight,
				ArticleInfo: "",
				posTagsList: [],
				posTag: '',
				// 数据
				about_center: [],
				qq_down_shop:'0',
				// 文章没有数据
				no_list_data: false,
				isShowAd: false,
				// 检测文章是否需要开启激励视频
				dontADs: [],
				preview: '',
				// 评论
				boolShow: false,

				// #ifdef MP-WEIXIN
				account: [],
				// #endif
				paymanImg: '',

				// #ifdef MP-QQ
				opId: '',
				is_qq_openid: true,
				// #endif

				// 改变标题栏颜色
				hasSetBg: false
			}
		},

		// 监听滑动
		onPageScroll: function(e) {
			// 判断型号 显示不同标题栏高度
			var that = this;
			if (e.scrollTop > 240) {
				that.isShowAd = true
			} else {
				that.isShowAd = false
			}
		},

		onLoad(posID) {
			// 接受数据
			this.posdCenterID = posID.id;
			// 界面打开 加载获取文章内容函数
			// this.posdData();
			// 界面打开 加载自定义标题高度函数
			this.topMenu();

			// 自定义php接口请求
			this.Getres();


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
			//检测qq小程序是否有open-id 如果没有就隐藏客服按钮
			this.getQOpenId();
			//#endif

		},

		// 分享好友配置
		onShareAppMessage(res) {
			var that = this;
			if (res.from === 'button') { // 来自页面内分享按钮
				console.log(res.target)
			}
			return {
				title: that.detailData.title.rendered,
				imageUrl: that.detailData.thumbnailurl,
				path: '/pages/data/data?id=' + this.posdCenterID
			}
		},
		// 文章分享盆友圈 目前支持安卓
		onShareTimeline(res) {
			var that = this;
			return {
				title: that.detailData.title.rendered,
				imageUrl: that.detailData.thumbnailurl,
				path: '/pages/data/data?id=' + this.posdCenterID
			}
		},

		methods: {
			// 自定义php接口请求
			Getres: function() {
				var that = this;

				// 请求基本数据
				uni.request({
					url: API + '/wp-json/wp/v2/fengrui',
					success: (res) => {
						// 数据
						that.about_center = res.data;
						//#ifdef MP-QQ
						that.qq_down_shop = res.data[0].xs_qq_off_down;
						//#endif
						
						// 获取文章内容
						that.posdData();
					}
				});
			},



			// 文章数据
			posdData: function() {
				var that = this;
				uni.request({
					url: API + '/wp-json/wp/v2/posts/' + that.posdCenterID,
					// url: 'https://blog.guoyiwl.com/wp-json/wp/v2/posts/347',
					success: (res) => {
						that.detailData = res.data;
						that.posdCenterTitle = res.data.title.rendered;
						that.posTag = res.data.tags[0];

						// 检测是否有标签和热门文章
						if (res.data.tags == '' || res.data.tags[0] == undefined) {
							console.log('该文章没有设置标签，请注意wordpress后天文章标签设置')
							that.no_list_data = true;
						} else {
							// 热门文章
							uni.request({
								url: API + '/wp-json/wp/v2/posts/?tags=' + that.posTag,
								success: (res) => {
									that.no_list_data = false;
									that.posTagsList = res.data.slice(0, 4);
								}
							});
						};
						// 检测是否有流量主
						//#ifdef MP-WEIXIN
						if (that.about_center[0].wx_jili_video != '' && that.detailData.fr_videp_if !=
							'1') {
							that.videoAD();
						} else {
							console.log('阅读该文章不需要激励视频广告')
						};
						//#endif

						//#ifdef MP-QQ
						if (that.about_center[0].wx_jili_video != '' && that.detailData.fr_videp_if !=
							'1') {
							that.videoAD();
						} else {
							console.log('阅读该文章不需要激励视频广告')
						};
						//#endif

						that.CreateAd();

						// 独立文章公告弹窗
						if (that.detailData.pos_notice != '') {
							uni.showModal({
								title: '公告',
								content: that.detailData.pos_notice,
								success: function(res) {
									if (res.confirm) {
										console.log('用户点击确定');
									} else if (res.cancel) {
										console.log('用户点击取消');
									}
								}
							});
						} else {
							console.log('该文章没有设置独立公告')
						};
					}
				});

			},

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

			// 左上角返回按钮
			tarBlack: function() {
				// 建立页面栈 如果是1则是分享打开需要返回主页，大于1则返回上一级
				var selPage = getCurrentPages();
				// console.log(selPage.length)
				if (selPage.length == 1) {
					uni.switchTab({
						url: "../index/index"
					})
				} else {
					uni.navigateBack({
						delta: 1
					});
				}
			},

			// 赞赏图片弹出
			tapMoney: function() {
				var that = this;
				//#ifdef MP-WEIXIN
				if (that.about_center[0].payman_img != '') {
					uni.previewImage({
						current: 1,
						urls: [that.about_center[0].payman_img],
						longPressActions: {
							itemList: ['发送给朋友', '保存图片', '收藏'],
							success: function(data) {
								console.log('选中了第' + (data.tapIndex + 1) + '个按钮,第' + (data.index + 1) +
									'张图片');
							},
							fail: function(err) {
								console.log(err.errMsg);
							}
						}
					});

				} else {
					console.log('您没有有配置赞赏图')
				}
				//#endif

				//#ifdef MP-QQ
				uni.showModal({
					title: '多端说明',
					content: '该操作不支持QQ端！！！',
					success: function(res) {
						if (res.confirm) {
							console.log('用户点击确定');
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					}
				});
				//#endif

			},

			// 点击跳转
			posTap: function(posd) {
				var posId = posd;
				console.log(posId)
				uni.navigateTo({
					url: '../data/data?id=' + posId,
				})
			},

			// 文章标签
			potgsTap: function(e) {
				var that = this;
				var jstag = e;
				uni.navigateTo({
					url: '../category/categ_lisst?jstag=' + jstag
				})
			},

			// 点击提交问题
			questionsTap: function() {
				var that = this;
				var posId = that.posdCenterID;
				uni.navigateTo({
					url: '../questions/questions?id=' + posId
				})
			},

			// 访问微信文章
			tapWx: function(wx) {
				var that = this;
				//#ifdef MP-WEIXIN
				var sve = wx;
				uni.navigateTo({
					url: '../weblist/weblist?aul=' + sve
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

			// 打开小商店详情
			tapShop: function() {
				var that = this;
				//#ifdef MP-WEIXIN
				uni.navigateToMiniProgram({
					appId: that.about_center[0].xs_shop_appid,
					path: 'plugin-private://wx34345ae5855f892d/pages/productDetail/productDetail?productId=' +
						that.detailData.xsgoshop,
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

			// 流量主视频弹窗
			videoAD: function() {
				var that = this;
				uni.showModal({
					title: '阅读说明',
					content: '感谢您阅读，但该文章需要您先阅读激励视频广告！！！',
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
			},

			// 复制下载地址
			tapDown: function(dn) {
				var that = this;
				that.dnurl = dn;
				console.log(that.dnurl)
				// 判断 如果文章不需要激励视频则下载资源需要看广告,如果文章需要阅读激励视频则直接下载资源
				if (that.detailData.if_rec_down_video != "1") {
					uni.showModal({
						title: '自动复制',
						content: '链接自动复制，请到浏览器下载',
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
				} else {
					uni.showModal({
						title: '下载提示',
						content: '观看激励视频后自动复制',
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
								if (that.detailData.fr_videp_if != '1') {
									uni.setClipboardData({
										data: that.dnurl,
										success: function() {
											console.log('复制内容成功');
										}
									});
								}
								uni.showToast({
									icon: 'none',
									title: "感谢您的支持"
								})
							} else {
								uni.showToast({
									icon: 'none',
									title: "中途关闭广告"
								})
								that.tarBlack();
							}
						})
					}

				} else {
					console.log('微信没有激励视频广告id')
				}
			},

			// 激励视频
			getVideoAd: function() {
				let that = this;

				//#ifdef MP-WEIXIN
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
				//#endif

				//#ifdef MP-QQ
				// 检测是否有激励视频id
				if (that.about_center[0].qq_jili_video != '') {
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
							console.log(that.detailData.fr_videp_if)
							// if (that.detailData.fr_videp_if != '1') {
							// 	uni.setClipboardData({
							// 		data: that.dnurl,
							// 		success: function() {
							// 			console.log(that.dnurl + '视频光看成功');
							// 		}
							// 	});
							// }
						} else {
							uni.showToast({
								icon: 'none',
								title: "中途关闭广告"
							})
							that.tarBlack();
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
					console.log('没有QQ激励视频广告id')
				}
				//#endif
			},

			// #ifdef MP-QQ
			//检测qq小程序是否有open-id 如果没有就隐藏客服按钮
			getQOpenId: function() {
				var that = this;
				uni.request({
					url: Getresources,
					success: (res) => {
						that.opId = res.data[8];
						if (that.opId.length == '') {
							console.log('后台qq小程序open-id是空的，客服按钮以隐藏')
							that.is_qq_openid = false;
						} else if (that.opId[0].opIdCode == '') {
							console.log('后台qq小程序open-id是空的，客服按钮以隐藏')
							that.is_qq_openid = false;
						} else {
							console.log('后台已经设置qq小程序open-id')
							that.is_qq_openid = true;
						}

					}
				});
			},

			// 权限申请
			addFr: function() {
				// 可以通过 qq.getSetting 先查询一下用户是否授权了 "scope.record" 这个 scope
				qq.getSetting({
					success(res) {
						if (!res.authSetting['setting.addFriend']) {
							qq.authorize({
								scope: 'setting.addFriend',
								success() {
									// 用户已经同意小程序使用录音功能，后续调用 qq.startRecord 接口不会弹窗询问
									qq.startRecord()
								}
							})
						}
					}
				})
			},
			//#endif

			// 点击回到主页那妞
			getIndex: function() {
				uni.switchTab({
					url: "../index/index"
				})
			},

			// 关注微信公众号
			accountTap: function(e) {
				var that = this;
				var sve = e;
				uni.navigateTo({
					url: '../weblist/weblist?aul=' + sve
				})
			},

			// 附件点击
			enclosure: function() {
				setTimeout(() => {
					uni.pageScrollTo({
						scrollTop: 0,
						duration: 0
					});
				}, 50);
			},
			// 点击获取链接地址
			navigate(href, e) {
				uni.setClipboardData({
					data: href,
					success: function() {
						console.log('设置系统剪贴板成功');
						uni.showToast({
							title: '复制成功啦',
							duration: 1500
						});
					},
					fail: function() {
						console.log('设置系统剪贴板失败');
						uni.showToast({
							title: '复制失败咯',
							duration: 1500
						});
					}
				});
			},
		}
	}
</script>

<style>
	/* 弹窗 */
	.cover-pos-fu {
		background-color: #479fff;
		width: 120upx;
		border-radius: 10upx;
		color: #FFFFFF;
		height: 80upx;
		text-align: center;
		line-height: 80upx;
		margin-left: 40upx;
	}

	.cover-pos-que {
		background-color: #007AFF;
		width: 460upx;
		border-radius: 10upx;
		color: #FFFFFF;
		height: 80upx;
		text-align: center;
		line-height: 80upx;
	}

	.cover-pos-view {
		position: fixed;
		bottom: 0upx;
		left: 0upx;
		width: 100%;
		background-color: #fafdfa;
		height: 140upx;
		border-radius: 20upx 20upx 0upx 0upx;
		display: flex;
		align-items: center;
		justify-content: center;
		z-index: 9999;
	}

	/* 评论 */
	.leaving-user-time {
		color: #D5D5D5;
		font-size: 20upx;
	}

	.leaving-user-info {
		margin-left: 20upx;
	}

	.leaving-user-img {
		background-color: #007AFF;
		border-radius: 10upx;
		height: 80upx;
		width: 80upx;
		flex-shrink: 0;
	}

	.leaving-view {
		background-color: #f8f8f8;
		border-radius: 10upx;
		padding: 20upx;
		margin: 30upx;
		display: flex;
	}

	/* me-svg */
	.Vajra-list-font {
		color: #807C7C;
		font-size: 24upx;
	}

	.Vajra-list-img {
		height: 60upx;
		width: 60upx;
		overflow: hidden;
		margin: auto;
	}

	.me-sbg-btn {
		width: 31%;
		/* overflow-y: hidden; */
		flex-shrink: 0;
		justify-content: center;
		align-items: center;
		padding-top: 24upx;
	}

	.me-svg {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin: 30upx;
		background-color: #FFFFFF;
		padding: 30upx 0upx 20upx 0upx;
		border-radius: 16upx;
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
		margin: 30upx;
		background-color: #fff;
		padding: 30upx;
		border-radius: 20upx;
		box-shadow: 0 8px 16px 0 rgba(0, 0, 0, 0.10);
	}

	.list-li {
		display: flex;
		align-items: center;
		margin: 20upx 0upx;
	}

	/* 关注微信公众号 */
	.account-aout {
		margin-left: 30upx;
	}

	.account-btn {
		height: 60upx;
		background-color: #007AFF;
		border-radius: 200upx;
		font-size: 24upx;
		width: 150upx;
		color: #FFFFFF;
		flex-shrink: 0;
		margin-right: 0upx;
	}

	.account-describe {
		font-size: 20upx;
		color: #7c7c7c;
	}

	.account-img {
		height: 80upx;
		width: 80upx;
		border-radius: 200upx;
		overflow: hidden;
	}

	.account-flex {
		display: flex;
	}

	.account-view {
		height: 120upx;
		border-radius: 20upx;
		background-color: #f7f7f7;
		margin: 0upx 28upx;
		display: flex;
		align-items: center;
		justify-content: space-between;
		padding: 0upx 18upx;
	}

	/* 没有数据时候 */
	.no-text {
		color: #ADADAD;
		margin-top: 40upx;
		font-size: 30upx;
	}

	.no-img {
		border-radius: 20upx;
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

	/* 列表没有数据 */
	.no-list-data {
		text-align: center;
		margin: 30upx 0px;
		color: #ADADAD;
	}

	/* 列表 */
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
		font-size: 26upx;
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-line-clamp: 2;
		-webkit-box-orient: vertical;
	}

	.list-li-left {
		margin-left: 32upx;
		flex-grow: 1;
		height: 116upx;
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
		margin: 20upx 0upx;
		align-items: center;
		border-radius: 20upx;
		justify-content: space-around;
	}

	.list-icon-list {
		text-align: center;
	}

	.list-icon-list-btn {
		text-align: center;
		margin: 0;
		background-color: unset;
		padding: 0;
	}

	.list-icon-list-img {
		height: 60upx;
		width: 60upx;
		overflow: hidden;

	}

	.list-icon-list-img-title {
		font-size: 22upx;
		color: #b1b1b1;
		margin-top: 12upx;
	}

	.list-li-toew-ma {
		margin: 40upx 0upx 20upx 0upx;
	}

	/* 标题 */
	.title-h-ad {
		height: 240upx;
		border-radius: 16upx;
		margin: 48upx;
		overflow: hidden;
	}

	.titel-h-go {
		font-size: 32upx;
		margin: 48upx;
	}

	/* 金刚区域 */
	.district-poster {
		width: 220upx;
		height: 60upx;
		border-radius: 200upx;
		background: linear-gradient(90deg, rgba(67, 130, 235, 1) 0%, rgba(6, 189, 254, 1) 100%);
		text-align: center;
		line-height: 64upx;
		color: #FFFFFF;
		font-size: 28upx;
		margin: 0upx 20upx;
		box-shadow: #4284ec 0upx 4upx 12upx;
	}

	.district-fenrid {
		width: 220upx;
		height: 60upx;
		border-radius: 200upx;
		background: linear-gradient(90deg, #F86168, #FFC6D3 100%);
		text-align: center;
		line-height: 64upx;
		color: #FFFFFF;
		font-size: 28upx;
		margin: 0upx 20upx;
		box-shadow: #F86168 0upx 4upx 12upx;
	}

	.district-money {
		width: 220upx;
		height: 60upx;
		border-radius: 200upx;
		background: linear-gradient(90deg, rgba(12, 185, 162, 1) 0%, rgba(15, 236, 210, 1) 100%);
		text-align: center;
		line-height: 64upx;
		color: #FFFFFF;
		font-size: 28upx;
		margin: 0upx 20upx;
		box-shadow: #0bbda6 0upx 4upx 12upx;
	}

	.district-fabulous {
		width: 180upx;
		height: 60upx;
		border-radius: 200upx;
		background: linear-gradient(90deg, rgba(248, 97, 104, 1) 0%, rgba(255, 198, 211, 1) 100%);
		text-align: center;
		line-height: 64upx;
		color: #FFFFFF;
		font-size: 28upx;
		margin: 0upx 16upx;
		box-shadow: #f96a71 0upx 6upx 22upx;
	}

	.district {
		display: flex;
		align-items: center;
		justify-content: center;
	}

	/* 评语 */
	.span-color {
		color: red;
	}

	.review {
		background-color: #f9f9f9;
		padding: 32upx;
		margin: 20upx 30upx;
		border-radius: 10upx;
		color: #807C7C;
		font-size: 24upx;
	}

	/* 正文内容 */
	.data-font {
		margin-top: 40upx;
	}

	.data-h:after {
		content: '';
		position: absolute;
		bottom: -14upx;
		left: 0px;
		width: 96upx;
		height: 8upx;
		border-radius: 200upx;
		background: linear-gradient(90deg, rgba(55, 110, 234, 1) 0%, rgba(255, 255, 255, 1) 100%);
	}

	.data-h {
		color: #000000;
		font-size: 32upx;
		position: relative;
	}

	.data-img {
		border-radius: 10upx;
		width: 100%;
		height: auto;
		overflow: hidden;
		margin: 30upx 0upx;
	}

	.data-view {
		margin: 20upx 30upx;
		font-size: 30upx;
		color: #333232;
		line-height: 60upx; //文字行高

	}

	/* 时间和浏览量 */

	.time-le {
		margin-left: 20upx;
	}

	.time-font {
		font-weight: 400;
		font-size: 22upx;
		color: rgba(124, 124, 124, 1);
		margin-left: 10upx;
	}

	.time-img {
		height: 36upx;
		width: 36upx;
		/* overflow: hidden; */
	}

	.time-view {
		margin: 30upx 48upx;
		display: flex;
		align-items: center;
		justify-content: flex-start;
	}

	/* 标题 */
	.tar-w-h {
		color: #000000;
	}

	.titel-h {
		font-size: 38upx;
		font-weight: bold;
	}

	.titel-view {
		width: 180upx;
		flex-shrink: 0;
	}

	.titel-h-w {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin: 50upx 30upx 0upx 30upx;
		flex-grow: 1;
	}

	/* 背景图片 */
	.fengrui-img-tar {
		height: 40upx;
		width: 40upx;
	}

	.square-black-img {
		height: 40upx;
		width: 40upx;
		margin-top: 10upx;
	}

	.square-black {
		position: fixed;
		left: 28upx;
		background: rgba(255, 255, 255, 0.5);
		height: 55upx;
		z-index: 2;
		width: 100upx;
		border-radius: 100upx;
		top: 0upx;
		text-align: center;
	}

	.back-radius {
		position: absolute;
		bottom: 0;
		left: 0;
		height: 60upx;
		width: 100%;
		border-radius: 40upx 40upx 0upx 0upx;
		background: #FFFFFF;
		z-index: 2;
	}

	.back-img {
		overflow: hidden;
		position: relative;
		background-color: #3482e2;
		border-radius: 0upx 0upx 40upx 40upx;
		padding-bottom: 20upx;
	}

	.fengrui {
		height: 100%;
		width: 100%;
	}

	/* 自定义标题栏 */
	.tar-img {
		margin-left: 28upx;
		height: 48upx;
		width: 48upx;
		overflow: hidden;
	}

	.tar-t {
		background-color: #FFFFFF;
		position: fixed;
		left: 0;
		width: 100%;
		z-index: 9999;
		display: flex;
		align-items: center;
		justify-content: space-between;
	}

	.tar-w {
		background-color: #FFFFFF;
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		z-index: 99999999999;
	}

	.fengrui-img {
		height: 100%;
		width: 100%;
	}

	.index-ov {
		/* overflow-x: hidden; */
	}

	button:after {
		border: 0px solid rgba(0, 0, 0, .2);

	}

	/* 富文本解析视频宽度 */
	.wp-video {
		width: 100% !important;
	}

	/* 暗黑模式下应用的样式 */
	@media (prefers-color-scheme: dark) {
		page {
			background: #161616;
		}

		.back-radius {
			background: #161616;
		}

		.tar-w {
			background: #161616;
		}

		.tar-t {
			background: #161616;
		}

		.data-h {
			color: #d7d7d7;
		}

		.data-view {
			color: #d7d7d7 !important;
		}

		.cover-pos-view,
		.account-view {
			background: #202020;
		}

		.me-svg {
			background: #202020;
		}

		.list-view {
			background: #202020;
		}

		.time-font {
			color: rgba(124, 124, 124, 1);
		}

		.fengrui-img-tar {
			color: rgba(201, 201, 201, 1);
		}

		.square-black-img {
			color: rgba(201, 201, 201, 1);
		}

		.tar-w-h {
			color: rgba(201, 201, 201, 1);
		}

		.wxParse .h1,
		.wxParse .h2,
		.wxParse .h3,
		.wxParse .h4,
		.wxParse .h5,
		.wxParse .h6,
		.wxParse .b,
		.wxParse .strong {
			font-weight: bolder;
			color: rgba(201, 201, 201, 1);
			font-size: 18px;
			position: relative;
		}


		.wxParse .h1:after,
		.wxParse .h2:after,
		.wxParse .h3:after,
		.wxParse .h4:after,
		.wxParse .h5:after,
		.wxParse .h6:after {
			content: '';
			position: absolute;
			bottom: -14upx;
			left: 0px;
			width: 96upx;
			height: 8upx;
			border-radius: 200upx;
			background: linear-gradient(90deg, rgba(55, 110, 234, 1) 0%, rgba(255, 255, 255, 1) 100%);
		}

		.wxParse {
			color: rgba(201, 201, 201, 1);
		}


	}
</style>
