<template>
	<view class="tag-all">
		<!-- <span class="tag-w" v-for="(tgs,index) in tagsall" :key="index" @tap="tgsTap(tgs.id)" >
			{{ tgs.name }}
		</span> -->

		<view class="tag-view">
			<view class="tag-li" v-for="(tgs,index) in tagsall" :key="index" @tap="tgsTap(tgs.id)">
				<view class="tag-yuan" :style="{background:tag_color[index]}"></view>
				{{ tgs.name }}
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
	export default {
		data() {
			return {
				tagsall: [],
				page: 1,
				// 文章没有数据
				no_list_data: false,
				tag_color: [
					'#ff0000', '#eb4310', '#f6941d', '#fbb417', '#ffff00', '#cdd541', '#99cc33', '#3f9337', '#219167', '#239676',
					'#24998d', '#1f9baa', '#0080ff', '#3366cc', '#333399', '#003366', '#800080', '#a1488e', '#c71585', '#bd2158',
					'#871F78', '#8E236B', '#D9D9F3', '#00FF00', '#6B238E', '#32CD99', '#5959AB', '#0000FF', '#2F4F4F', '#3232CD', 
					'#6F4242', '#FF00FF', '#97694F', '#6B8E23', '#BC1717', '#00FFFF', '#7093DB', '#EAEAAE', '#238E68', '#FFFF00', 
					'#855E42', '#9370DB', '#6B4226', '#545454', '#426F42', '#8E6B23', '#70DB93', '#856363', '#7F00FF', '#E6E8FA',
					'#5C3317', '#D19275', '#7FFF00', '#3299CC', '#9F5F9F', '#8E2323', '#70DBDB', '#007FFF', '#B5A642', '#238E23', 
					'#DB7093', '#FF1CAE', '#D9D919', '#CD7F32', '#A68064', '#00FF7F', '#A67D3D', '#DBDB70', '#2F2F4F', '#236B8E',
					'#8C7853', '#C0C0C0', '#23238E', '#38B0DE', '#A67D3D', '#527F76', '#4D4DFF', '#DB9370', '#5F9F9F', '#93DB70', 
					'#FF6EC7', '#D8BFD8', '#D98719', '#215E21', '#00009C', '#ADEAEA', '#B87333', '#4E2F2F', '#EBC79E', '#5C4033',
					'#FF7F00', '#9F9F5F', '#CFB53B', '#CDCDCD', '#42426F', '#C0D9D9', '#FF7F00', '#5C4033', '#A8A8A8', '#FF2400', 
					'#CC3299', '#2F4F2F', '#8F8FBD', '#DB70DB', '#D8D8BF', '#4A766E', '#E9C2A6', '#8FBC8F', '#99CC32', '#4F4F2F', 
					'#32CD32', '#BC8F8F',	
				]

			}
		},
		onLoad() {
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
			//#endif


			
		},

		// 监听触底
		onReachBottom() {
			this.page = this.page + 1;
			this.allTags();
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
				path: 'pages/tags/tags'
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
						// 获取标签
						this.allTags();
					}
				});
			},

			// 获取标签数据
			allTags: function() {
				var that = this;
				uni.request({
					url: API + '/wp-json/wp/v2/tags?per_page=50&page=' + this.page,
					success: (res) => {
						console.log(res.data)
						if (res.data.length == '') {
							that.no_list_data = true;
						} else {
							that.no_list_data = false;
							that.tagsall = that.tagsall.concat(res.data);
						}
					}
				});
			},

			// 点击标签跳转
			tgsTap: function(e) {
				var that = this;
				var jstag = e;
				console.log(jstag)
				uni.navigateTo({
					url: '../category/categ_lisst?jstag=' + jstag
				})
			}
		}
	}
</script>

<style>
	.tag-yuan {
		height: 10upx;
		width: 30upx;
		background-color: #000000;
		border-radius: 100upx;
		position: absolute;
		top: 20upx;
		left: 20upx;
	}

	/* 标签样式 */
	.tag-li {
		background-color: #FFFFFF;
		width: 30%;
		flex-shrink: 0;
		border-radius: 10upx;
		height: 120upx;
		margin: 8upx;
		text-align: center;
		display: flex;
		flex-wrap: wrap;
		align-items: center;
		justify-content: center;
		font-size: 24upx;
		position: relative;
	}

	.tag-view {
		display: flex;
		flex-flow: wrap;
		width: 100%;
	}

	/* 列表没有数据 */
	.no-list-data {
		text-align: center;
		margin: 30rpx 0px;
		color: #ADADAD;
		font-size: 20upx;
	}

	.tag-w {
		height: 60upx;
		line-height: 60upx;
		border-radius: 10upx;
		border: 1px #ececec solid;
		background-color: #ececec;
		padding: 0upx 40upx;
		font-size: 30upx;
		color: #999;
		margin: 20upx;
	}

	.tag-all {
		margin: 48upx;
		display: flex;
		flex-wrap: wrap;
		justify-content: flex-start;
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

		.tag-li {
			background: #202020
		}

	}
</style>

