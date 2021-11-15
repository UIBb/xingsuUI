<template>
	<view class="center">
		<!-- 顶部图片 -->
		<view class="about-w">
			<image class="fengrui-img" :src="about_center.fr_logo" mode="aspectFit"></image>
		</view>
		
		<!-- 标题 -->
		<view class="data-h ma-top">
			{{ about_center.title.rendered}}
		</view>
		
		<!-- 标语 -->
		<view class="data-ad-Reading">
			晚点遇见吧！你刚好成熟我刚好温柔
		</view>
		<view class="data-view">
			<view class="data-font">
				<u-parse :content="about_center.content.rendered" :loading="loading" @preview="preview" @navigate="navigate" />
			</view>
		</view>
		
		<!-- 版本说明 -->
		<view class="author">
		 架构：星宿UI V1.1.x  发布于: 枫瑞博客网 作者: yinfengrui 协议: MIT
		</view>
	</view>
</template>

<script>
	// 引入域名和自定义php文件
	import {
		API
	} from '@/components/api.js';
	import uParse from '@/components/gaoyia-parse/parse.vue'
	export default {
		components: {
			uParse
		},
		data() {
			return {
				// 关于数据
				about_center:'',
			}
		},
		onLoad() {
			// 自定义php接口请求
			this.getabout();
			
			// 微信分享盆友圈
			//#ifdef MP-WEIXIN
			wx.showShareMenu({
				withShareTicket: true,
				menus: ['shareAppMessage', 'shareTimeline']
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
				path: 'pages/about/about'
			}
		},
		
		methods: {
			// 关于数据获取
			getabout:function(){
				var that = this;
				uni.request({
					url: API + '/wp-json/wp/v2/fengrui',
					success: (res) => {
						
						
						// 关于数据
						that.about_center = res.data[0]
						console.log(that.about_center)
					}
				});
			}
			
		}
	}
</script>

<style>
	/* 版权说明 */
	.author{
		font-size: 20upx;
		color: #484848;
		text-align: center;
		color: #a9a8a8;
	}
	
	/* 内容 */
	.data-view {
		margin: 48upx 0upx;
		font-size: 28upx;
		color: #333232;
		line-height: 60upx;
		background-color: #FFFFFF;
		border-radius: 20upx;
		padding: 20upx 30upx;
	}
	/* 标语 */
	.data-ad-Reading {
		font-size: 24rpx;
		letter-spacing: 3px;
		color: #8d9090;
		margin-top: 10upx;
	}
	
	/* 标题 */
	.ma-top{
		margin-top: 60upx;
	}
	.data-h {
		color: #000000;
		font-size: 36upx;
		position: relative;
		
	}
	
	/* 顶部图片 */
	.about-w{
		height: 320upx;
		width: 100%;
		position: relative;
		border-radius: 20upx;
		overflow: hidden;
		box-shadow: #dcdcdc 2UPX 6UPX 12UPX; 
	}
	
	
	/* 全局 */
	.center {
		margin: 48upx;
	}
	.fengrui-img {
		height: 100%;
		width: 100%;
	}
	page {
		background-color: #F7F7F7;
	}
	
	/* 暗黑模式下应用的样式 */
	@media (prefers-color-scheme: dark) {
		page {
			background: #161616;
		}
		.data-view{
			background-color: #202020;
		}
		.author{
			font-size: 20upx;
			color: #484848;
			text-align: center;
			color: rgba(201, 201, 201, 1);
		}
	}
</style>
