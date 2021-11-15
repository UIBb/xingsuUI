<template>
	<view class="container">
		<!-- 更多列表 -->
		<block v-for="(wxpos ,index) in WxlistMore" :key="index" >
			<block v-if="(index +1 ) == 1">
				<view class="list-one-w" @tap="wxlistTap(wxpos.hurl)">
					<view class="list-one-img">
						<image class="fengrui-img" mode="aspectFill" :src="wxpos.himg"></image>
					</view>
					<view class="list-one-data-w">
						<view class="list-one-title">
							{{ wxpos.title }}
						</view>
						<view class="list-ona-abstract">
							{{ wxpos.describe }}
						</view>
					</view>
				</view>
			</block>
			
			<!-- 左文右图样式 -->
			<view class="list-float-bg" @tap="wxlistTap(wxpos.hurl)">
				<view class="">
					<view class="list-float-text">
						{{ wxpos.title }}
					</view>
					<view class="list-ona-abstract-2">
						{{ wxpos.describe }}
					</view>
				</view>
				<view class="list-float-img">
					<image class="fengrui-img" :src="wxpos.himg" mode="aspectFill"></image>
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
	import {
		Getwatch
	} from '@/components/api.js';
	export default {
		data() {
			return {
				WxlistMore:[],
				XcxMessage:[]
			}
		},
		onLoad(option) {
			// 保存参数
			this.wxmore = option.wxmore;
			this.getWxlisst();
		},
		methods: {
			
			// 更多点击
			getWxlisst:function(){
				var that = this;
				uni.request({
					url: Getwatch,
					success: (res) => {
						that.WxlistMore = res.data[0];
					}
				});
			},
			
			// 点击列表打开文章
			wxlistTap:function(e){
				console.log(e)
				var that = this;
				var sve = e;
				uni.navigateTo({
					url: '../weblist/weblist?aul=' + sve
				})
			}
		}
	}
</script>

<style>
	/* 左文右图样式 */
	.list-float-img{
		width: 100upx;
		height: 100upx;
		overflow: hidden;
		flex-shrink: 0;
		border-radius: 10upx;
	}
	.list-float-text{		
		font-size: 24upx;
		overflow: hidden;
		height: 60upx;
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-line-clamp: 2;
		-webkit-box-orient: vertical;
		margin-top: 16upx;
		width: calc(100% - 90upx);
	}
	.list-float-bg{
		background-color: #FFFFFF;
		border-radius: 20upx;
		padding: 30upx;
		display: flex;
		margin-top: -36upx;
		justify-content: space-between;
	}
	
	/* 文章列表单排 */
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
	.list-ona-abstract-2 {
		color: #ADADAD;
		font-size: 24upx;
		overflow: hidden;
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-line-clamp: 1;
		-webkit-box-orient: vertical;
		margin-top: 16upx;
		    width: calc(100% - 90rpx);
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
		margin: 0upx 0upx 60upx 0upx;
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
	
	
	page{
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
		}
	}
</style>
