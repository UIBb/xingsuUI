<template>
	<view class="">
		<!-- 判断是否登入 -->
		<block v-if="isLogin == true">
			<!-- 顶部图片 -->
			<view class="top-img">
				<image class="fengrui-img" src="../../static/my/bg15-1.svg" mode="aspectFill"></image>
			</view>

			<!-- 头像 -->
			<view class="self-w">
				<view class="self-user-img">
					<image class="fengrui-img" :src="xingsu.avatarUrl" mode="aspectFill"></image>
				</view>
				<view class="self-user-info">
					<view class="self-user-info-name">
						{{ xingsu.nickName }}
					</view>
					<view class="self-user-info-describe">
						青春里的温柔一定与你有关
					</view>
				</view>
				<view class="self-user-updata">
					<button class="self-user-updata-btn" openType="getUserInfo" lang="zh_CN"
						@tap="updatauser()">更新</button>
				</view>
			</view>

			<!-- me-svg -->
			<view class="me-svg">
				<button class="me-sbg-btn" @tap="allTag()">
					<view class="Vajra-list-img">
						<image class="fengrui-img" src="../../static/my/star.png" mode="aspectFill"></image>
					</view>
					<view class="Vajra-list-font">
						知识星球
					</view>
				</button>
				<button class="me-sbg-btn" @tap="popupSever()">
					<view class="Vajra-list-img">
						<image class="fengrui-img" src="../../static/my/poster.png" mode="aspectFill"></image>
					</view>
					<view class="Vajra-list-font">
						分享海报
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

			<!-- 流量主广告或者图片 -->
			<view class="me-ads">
				<!-- #ifdef MP-WEIXIN -->
				<block v-if="about_center.length>0">
					<view class="">
						<ad :unit-id="about_center[0].wx_gezi" ad-type="grid" grid-opacity="0.8" grid-count="5"
							ad-theme="white"></ad>
					</view>
				</block>
				<!-- #endif -->
			</view>

			<!-- 功能列表 -->
			<view class="metergasis">
				<view class="metergasis-li" @tap="clearTap()">
					<view class="metergasis-li-img">
						<image class="fengrui-img" src="../../static/my/cache.png" mode="aspectFill"></image>
					</view>
					<view class="metergasis-li-h">
						清除缓存
					</view>
					<view class="metergasis-li-rgth">
						<image class="fengrui-img" src="../../static/my/more.svg" mode=""></image>
					</view>
				</view>

				<view class="metergasis-li" @tap="abouTap()">
					<view class="metergasis-li-img">
						<image class="fengrui-img" src="../../static/my/about.png" mode="aspectFill"></image>
					</view>
					<view class="metergasis-li-h">
						关于程序
					</view>
					<view class="metergasis-li-rgth">
						<image class="fengrui-img" src="../../static/my/more.svg" mode=""></image>
					</view>
				</view>

				<view class="metergasis-li" @tap="clearTap()">
					<view class="metergasis-li-img">
						<image class="fengrui-img" src="../../static/my/exit.png" mode="aspectFill"></image>
					</view>
					<view class="metergasis-li-h">
						退出登录
					</view>
					<view class="metergasis-li-rgth">
						<image class="fengrui-img" src="../../static/my/more.svg" mode=""></image>
					</view>
				</view>
			</view>
		</block>
		<block v-if="isLogin == false">
			<view class="login-w">
				<view class="login-img">
					<image class="feng-img" src="../../static/my/nologin.svg" mode="widthFix"></image>
				</view>

				<view class="login-btn">
					<button class="login-btn-lo" openType="getUserInfo" lang="zh_CN" @tap="getUserInfo">登录</button>
				</view>
			</view>
		</block>
	</view>
</template>

<script>
	// 引入域名和自定义php文件
	import {
		API
	} from '@/components/api.js';
	export default {
		data() {
			return {
				isLogin: false,
				xingsu: [],
				about_center: [],

				// #ifdef MP-QQ
				opId: '',
				is_qq_openid: false,
				// #endif
			}
		},
		onLoad() {
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

			// 判断是否有登入
			this.checkLogin();
			// 自定义php接口请求
			this.Getres();


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
				path: 'pages/me/me'
			}
		},

		methods: {
			// 自定义php接口请求
			Getres: function() {
				var that = this;
				uni.request({
					url: API + '/wp-json/wp/v2/fengrui',
					success: (res) => {
						// 关于数据
						that.about_center = res.data;
					}
				});
			},

			// 点击更新
			updatauser: function() {
				var that = this;
				that.getUserInfo();
				uni.showToast({
					title: '用户信息已更新',
					duration: 2000
				});

			},

			// 用户登录
			getUserInfo: function() {
				var that = this;
				//#ifdef MP-QQ
					uni.getUserInfo({
						success: function(infoRes) {
							console.log(infoRes)
							that.xingsu = infoRes.userInfo;
							that.isLogin = true;
							uni.setStorageSync("xsUserInfo_key", that.xingsu);
						}
					});
				//#endif
				
				//#ifdef MP-WEIXIN
					uni.getUserProfile({
						desc: '用于完善会员资料', // 声明获取用户个人信息后的用途，后续会展示在弹窗中，请谨慎填写
						success: (res) => {
							console.log(res.userInfo)
							that.xingsu = res.userInfo;
							that.isLogin = true;
							uni.setStorageSync("xsUserInfo_key", that.xingsu);
						}
					})
				//#endif
			},

			// 检测是否有登入  赋值给xsUserInfo判断是否为空
			checkLogin: function() {
				var that = this;
				var xsUserInfo = uni.getStorageSync('xsUserInfo_key');
				if (xsUserInfo == '') {
					this.isLogin = false;

				} else {
					this.isLogin = true;
					that.xingsu = xsUserInfo;
				}
			},

			// 清除缓存
			clearTap: function() {
				uni.clearStorageSync();
				uni.clearStorage();
				uni.showToast({
					title: '清理成功',
					duration: 2000
				})
				uni.switchTab({
					url: '../index/index'
				})
				this.isLogin = false;
			},

			// 标题页跳转
			allTag: function() {
				uni.navigateTo({
					url: '../tags/tags'
				})
			},
			abouTap: function() {
				uni.navigateTo({
					url: '../about/about'
				})
			},

			// 分享海报
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

			// #ifdef MP-QQ
			//检测qq小程序是否有open-id 如果没有就隐藏客服按钮
			getQOpenId: function() {
				var that = this;
				// uni.request({
				// 	url: Getresources,
				// 	success: (res) => {
				// 		that.opId = res.data[8];
				// 		console.log(that.opId)
				// 		if (that.opId.length == '') {
				// 			console.log('后台qq小程序open-id是空的，客服按钮以隐藏')
				// 			that.is_qq_openid = false;
				// 		} else if (that.opId[0].opIdCode == '') {
				// 			console.log('后台qq小程序open-id是空的，客服按钮以隐藏')
				// 			that.is_qq_openid = false;
				// 		} else {
				// 			console.log('有id了')
				// 			that.is_qq_openid = true;
				// 		}

				// 	}
				// });
			},

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
			// #endif
		}

	}
</script>

<style>
	/* 顶部图片 */
	.top-img {
		width: 100%;
		height: 300upx;
		overflow: hidden;
	}

	/* 登陆框 */
	.login-btn-lo {
		background-color: #6999fc;
		border-radius: 100upx;
		height: 100upx;
		width: 550upx;
		line-height: 100upx;
		color: #FFFFFF;
	}

	.login-btn-qu {
		background-color: #FD6D6D;
		border-radius: 100upx;
		height: 80upx;
		width: 300upx;
		line-height: 80upx;
		color: #FFFFFF;
	}

	.login-btn {
		display: flex;
		justify-content: center;
		align-items: center;
		padding: 0upx 30upx;
		margin-top: 80upx;

	}

	.feng-img {
		height: 100%;
		margin: auto;
		display: block;
	}

	.login-img {
		height: 700upx;
		overflow: hidden;
		border-radius: 20upx;
		position: relative;
		width: 100%;
	}

	.login-w {
		/* margin-top: 40%; */
		width: 76%;
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
	}

	/* 功能列表 */
	.metergasis-li-rgth {
		width: 50upx;
		height: 50upx;
		overflow: hidden;
		flex-shrink: 0;
		position: absolute;
		right: 0upx;
		top: 50%;
		transform: translate(0%, -50%);
	}

	.metergasis-li-h {
		grid-row: 1;
		margin-left: 30upx;
		font-size: 30upx;
	}

	.metergasis-li-img {
		height: 52upx;
		width: 52upx;
		border-radius: 100upx;
		flex-shrink: 0;
	}

	.metergasis-li {
		margin: 48upx 32upx;
		display: flex;
		justify-content: flex-start;
		align-items: center;
		position: relative;
	}

	.metergasis {
		background-color: #FFFFFF;
		border-radius: 16upx;
		margin: 48upx 0upx;
		padding: 2upx;
		margin: 30upx;
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
	}

	.me-svg {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin: 30upx;
		background-color: #FFFFFF;
		padding: 30rpx 0rpx 20rpx 0rpx;
		border-radius: 16upx;
	}

	/* 流量主广告或者图片 */
	.me-ads {
		border-radius: 18upx;
		margin: 48upx 0upx;
		overflow: hidden;
		margin: 30upx;
	}

	/* 头像 */
	.self-user-updata-btn {
		border-radius: 100upx;
		width: 128upx;
		height: 48upx;
		line-height: 48upx;
		font-size: 26upx;
		background: linear-gradient(90deg, rgba(67, 130, 235, 1) 0%, rgba(6, 189, 254, 1) 100%);
		color: #FFFFFF;
	}

	.self-user-updata {
		width: 128upx;
		flex-shrink: 0;
	}

	.self-user-info-describe {
		color: #807C7C;
		font-size: 24upx;
		margin-top: 18upx;
	}

	.self-user-info-name {
		font-size: 48upx;
		flex-grow: 1;
	}

	.self-user-info {
		margin: 0upx 24upx;
	}

	.self-user-img {
		height: 120upx;
		width: 120upx;
		border-radius: 100upx;
		/* margin-left: 30upx; */
		overflow: hidden;
		flex-shrink: 0;
		/* box-shadow: #F9F9F9 2px 6px 24px; */
	}

	.self-w {
		background-color: #FFFFFF;
		border-radius: 16upx;
		display: flex;
		justify-content: space-between;
		padding: 48upx 26upx;
		align-items: flex-end;
		overflow: hidden;
		margin: 0rpx 30upx;
	}

	/* 全局 */
	.center {
		margin: 48upx;
	}

	page {
		background-color: #f7f7f7;
		overflow-x: hidden;
	}

	.fengrui-img {
		height: 100%;
		width: 100%;
	}

	button {
		padding-left: 0upx;
		padding-right: 0upx;
		/* line-height: 0; */
		background-color: #FFFFFF;
	}

	button::after {
		border: 0px solid rgba(0, 0, 0, .2);

	}

	/* 暗黑模式下应用的样式 */
	@media (prefers-color-scheme: dark) {
		page {
			background: #161616;
		}

		.self-w {
			background-color: #202020;
		}

		.metergasis {
			background-color: #202020;
		}

		.me-svg {
			background-color: #202020;
		}

		.me-sbg-btn {
			background-color: #202020;
		}

		button {
			background-color: #202020;
		}
	}
</style>
