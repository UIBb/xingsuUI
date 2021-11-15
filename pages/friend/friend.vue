<template>
	<view class="center">
		<!-- 热评 写死的一句话 -->
		<view class="review" v-if="is_data_review">
			{{ hotreview }}
		</view>
		<!-- 微信小程序列表 -->
		<!-- #ifdef MP-WEIXIN -->
		<view class="friend-two-w">
			<view class="friend-two-friend" v-for="(pos ,index) in LinkList" :key="index">
				<navigator :path="pos.url" hover-class="none" open-type="navigate" :app-id="pos.appid" target="miniProgram">
					<view class="friend-two-friend">
						<view class="friend-two-img">
							<image class="fengrui-img" :src="pos.img" mode="aspectFill"></image>
						</view>
						<view class="friend-two-title">
							{{ pos.title}}
						</view>
						<view class="friend-two-abstract">
							{{pos.introduce}}
						</view>
					</view>
				</navigator>
			</view>
		</view>
		<!-- #endif -->
		
		<!-- QQ小程序列表  只能加10个有限制-->
		<!-- #ifdef MP-QQ -->
		<view class="friend-two-w">
			<view class="friend-two-friend" v-for="(pos ,index) in LinkList" :key="index" @tap="QQLinkList(index)">
				<view class="friend-two-friend">
					<view class="friend-two-img">
						<image class="fengrui-img" :src="pos.img" mode="aspectFill"></image>
					</view>
					<view class="friend-two-title">
						{{ pos.title}}
					</view>
					<view class="friend-two-abstract">
						{{pos.introduce}}
					</view>
				</view>
			</view>
		</view>
		<!-- #endif -->
		
		<!-- 没有内容 -->
		<view class="no-datalist" v-if="is_data">
			<view class="no-img">
				<image class="fengrui-img" src="../../static/data/data-no.svg" mode=""></image>
			</view>
			<view class="no-text">
				您的世界还没有盆友哦！或者和小姐姐跑了......
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
		Getfriend
	} from '@/components/api.js';
	export default {
		data() {
			return {
				is_data: false,
				is_data_review:true,
				hotreview: "曾经我自半个诗人，见山是深情伟岸，见海是热情澎湃，见花见草信他们皆有故事",
				LinkList: [],
				// 数据缓存
				about_center: [],
			}
		},
		
		onLoad() {
			// 自定义php接口请求
			this.Getres();
			// 获取友情链接
			this.getriendList();
			
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
		
		// 分享好友配置
		onShareAppMessage(res) {
			var that = this;
			if (res.from === 'button') { // 来自页面内分享按钮
				console.log(res.target)
			}
			return {
				title: that.about_center.share_title,
				imageUrl: that.about_center.share_title_url,
				path: 'pages/friend/friend'
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
					that.about_center = res.data[0]
					// console.log(that.about_center)
				}
			});
			},
			
			getriendList:function(){
				var that = this;
				uni.request({
					url: Getfriend,
					success: (res) => {
						// 获取微信友情链接
						//#ifdef MP-WEIXIN
						that.LinkList = res.data[0];
						//#endif
						
						// 获取QQ友情链接
						//#ifdef MP-QQ
						that.LinkList = res.data[1];
						//#endif
					}
				});
			},
			
			//#ifdef MP-QQ
			QQLinkList:function(e){
				var that = this;
				var index = e ;
				uni.navigateToMiniProgram({
				  appId: that.LinkList[index].appid,
				  path: 'pages/index/index',
				  success(res) {
					console.log('你正在打开'+that.LinkList[index].appid)
				  }
				})
			},
			//#endif
			
		}
	}
</script>

<style>
	
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
	
	
	/* 小程序列表 */

	.friend-two-abstract {
		color: #ADADAD;
		font-size: 24upx;
		overflow: hidden;
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-line-clamp: 2;
		-webkit-box-orient: vertical;
		margin:  10upx 40upx 40upx 40upx;
	}

	.friend-two-title {
		font-size: 30upx;
		overflow: hidden;
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-line-clamp: 2;
		-webkit-box-orient: vertical;
		margin: 0upx 40upx 0upx 40upx;
	}

	.friend-two-img {
		width: 100upx;
		height: 100upx;
		border-radius: 20upx;
		overflow: hidden;
		margin: 40upx;
	}

	.friend-two-friend {
		border-radius: 20upx;
		overflow: hidden;
		flex-shrink: 0;
		width: 46%;
		width: 300upx;
		height: 336upx;
		background-color: #FFFFFF;
		margin-bottom: 40upx;
		
		
	}

	.friend-two-w {
		margin: 60upx 0;
		display: flex;
		justify-content: space-between;
		align-items: center;
		flex-wrap: wrap;
	}

	/* 热评 */
	.review {
		background-color: #F0F0F0;
		padding: 32upx;
		border-radius: 10upx;
		color: #ADADAD;
		font-size: 24upx;

	}

	/* 全局 */
	.center {
		margin: 48upx 30upx;
	}

	page {
		background-color: #f7f7f7;
		overflow-x: hidden;
	}

	.fengrui-img {
		height: 100%;
		width: 100%;
	}
	
	/* 暗黑模式下应用的样式 */
	@media (prefers-color-scheme: dark) {
		page {
			background: #161616;
		}
		.review {
			background-color: #212121;
		}
		.friend-two-friend{
			background-color: #212121;
		}
	}
</style>
