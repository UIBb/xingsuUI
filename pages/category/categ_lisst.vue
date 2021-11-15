<template>
	<view class="container">
		<view class="" v-for="(pos ,index) in postList" :key="index" @click="posTap(pos.id)">
			<view class="list-one-w">
				<view class="list-one-img" v-if="pos.thumbnailurl == null">
					<image class="fengrui-img" mode="aspectFill" :src="about_center[0].xs_posts_img"></image>
				</view>
				<view class="list-one-img" v-else>
					<image class="fengrui-img" mode="aspectFill" :src="pos.thumbnailurl"></image>
				</view>
				<view class="list-one-data-w">
					<view class="list-one-title">
						{{ pos.title.rendered}}
					</view>
					<view class="list-ona-abstract">
						<rich-text :nodes="pos.excerpt.rendered"></rich-text>
					</view>
					<view class="list-one-qu">
						<view class="list-one-time">
							<view class="list-one-time-y"></view>
							<view class="list-one-time-n">
								时间：{{pos.date}}
							</view>
						</view>
						<view class="list-one-time">
							<view class="list-one-time-l"></view>
							<view class="list-one-time-n">
								权限：{{pos.comment_status}}
							</view>
						</view>
					</view>
				</view>
			</view>
			<!-- #ifdef MP-WEIXIN -->
			<block v-if="about_center.length>0">
				<ad v-if="(index +1) % 4 == 0" :unit-id="about_center[0].wx_video" ad-type="video" ad-theme="white"></ad>
			</block>
			<!-- #endif -->

			<!-- #ifdef MP-QQ -->
			<block v-if="about_center.length>0">
				<ad v-if="(index +1) % 4 == 0" :unit-id="about_center[0].qq_tie_video" type="card"></ad>
			</block>
			<!-- #endif -->

		</view>

		<!-- 没有内容 -->
		<view class="no-datalist" v-if="is_data">
			<view class="no-img">
				<image class="fengrui-img" src="../../static/data/data-no.svg" mode=""></image>
			</view>
			<view class="no-text">
				您搜索的世界不存在！或者和小姐姐跑了......
			</view>
		</view>

		<!-- 文章列表没有数据 -->
		<view class="no-list-data" v-if="no_list_data">
			--我的底线就到这里了--
		</view>
	</view>
</template>

<script>
	// 引入域名和自定义php文件
	import {
		API
	} from '@/components/api.js';
	let interstitialAd = null;
	export default {
		data() {
			return {
				// 文章列表
				postList: [],
				is_data: false,
				keyword: '',
				type: '',
				page: 1,
				no_list_data: false,

				about_center: [],
			}
		},
		onLoad(option) {
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
			// this.getQQChaping();
			//#endif



			// 判断不同界面过的来数据
			console.log(option)
			this.keyword = option.keyword
			this.type = option.type;
			this.jstag = option.jstag
			this.catepos = option.catepos

			// 判断数据类型 执行不同函数
			if (this.keyword) {
				this.getSearchList();
			} else if (this.jstag) {
				this.getTagAllList();
			} else {
				this.getCateposList();
			}



		},
		// 监听触底
		onReachBottom() {
			this.page = this.page + 1;
			if (this.keyword) {
				this.getSearchList();
			} else if (this.jstag) {
				this.getTagAllList();
			} else if (this.catepos) {
				this.getCateposList();
			}
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
				path: 'pages/categ_lisst/categ_lisst'
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
						this.getWxchaAd();
					}
				});
			},

			// 点击跳转
			posTap: function(posd) {
				var posId = posd;
				console.log(posId)
				uni.navigateTo({
					url: '../data/data?id=' + posId,
				})
			},

			// 搜索数据
			getSearchList: function() {
				var that = this;
				uni.request({
					url: API + '/wp-json/wp/v2/posts?search=' + that.keyword + '&page=' + that.page,
					success: (res) => {
						if (res.data.message == '请求的页码大于总页数。') {
							that.no_list_data = true;
						} else if (res.data.length == '') {
							that.is_data = true;
						} else {
							that.no_list_data = false;
							that.is_data = false;
							that.postList = res.data;
						}
					}
				});
			},

			// 标签数据
			getTagAllList: function() {
				var that = this;
				uni.request({
					url: API + '/wp-json/wp/v2/posts?tags=' + this.jstag + '&page=' + that.page,
					success: (res) => {
						console.log(that.postList);
						if (res.data.message == '请求的页码大于总页数。') {
							that.no_list_data = true;
						} else if (res.data.length == '') {
							that.is_data = true;
						} else {
							that.no_list_data = false;
							that.no_list_data = false;
							that.postList = that.postList.concat(res.data);
						}
					}
				});
			},

			// 文章分类数据
			getCateposList: function() {
				var that = this;
				uni.request({
					url: API + '/wp-json/wp/v2/posts?categories=' + this.catepos + '&page=' + that.page,
					success: (res) => {
						if (res.data.message == '请求的页码大于总页数。') {
							that.no_list_data = true;

						} else if (res.data.length == '') {
							that.is_data = true;
						} else {
							that.no_list_data = false;
							that.no_list_data = false;
							that.postList = that.postList.concat(res.data);
						}
					}
				});
			},

			// 插屏广告数据
			getWxchaAd: function() {
				var that = this;

				//#ifdef MP-WEIXIN	
					if(that.about_center[0].wx_chaping != ''){
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
					}else{
						console.log('微信没有配置插屏广告')
					}
				//#endif	

				//#ifdef MP-QQ
				if(about_center[0].qq_chaping != ''){
					let InterstitialAd = qq.createInterstitialAd({
						adUnitId: that.about_center[0].qq_chaping
					})
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
					/* 建议放在需要展示插屏广告的时机执行 */
					InterstitialAd.show().catch((err) => {
						console.error('show', err)
					})
				}else{
					console.log('QQ没有配置插屏广告')
				}
				//#endif
			},
		}
	}
</script>

<style>
	/* 列表没有数据 */
	.no-list-data {
		text-align: center;
		margin: 30rpx 0px;
		color: #ADADAD;
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


	/* 文章列表单排 */
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
		-webkit-line-clamp: 2;
		-webkit-box-orient: vertical;
		margin-top: 16upx;
	}

	.list-one-title {
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
		margin: 50upx 0upx;
		overflow: hidden;
		background-color: #FFFFFF;
	}

	.container {
		padding: 40upx;
	}

	.fengrui-img {
		height: 100%;
		width: 100%;
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

		.list-one-w {
			border: 1px #191919 solid;
			background: #202020;
		}
	}
</style>
