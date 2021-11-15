<template>
	<view>
		<view class="que-post-view">
			<view class="">
				
			</view>
		</view>
		
		<!-- 标题 -->
		<view class="titel-h-w">
			<view class="titel-h">
				其他问题
			</view>
		</view>
		
		<!-- 提问题列表 -->
		<view class="que-center" v-for="(item ,index) in CommentsList" >
			<view class="que-list-view">
				<view class="que-list-q">
					<image class="fengrui-img" src="../../static/data/comment.svg" mode="aspectFit"></image>
				</view>
				<view class="que-list-name">
					<span class="que-list-name-color">{{ item.author_name }}</span>提出问题
				</view>
			</view>
			<view class="que-list-center">
				<rich-text :nodes="item.content.rendered"></rich-text>
			</view>
			
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
				posrId:"",
				CommentsList:[],
				CommentsListTown:[]
			}
		},
		onLoad(e) {
			console.log(e)
			this.posrId = e.id;
			// 请求评论
			this.GetPostComments()
		},
		methods: {
			GetPostComments:function(){
				var that = this;
				uni.request({
					url: API + '/wp-json/wp/v2/comments?post=3247',
					success(res) {
						that.CommentsList = res.data;
						that.CommentsListTown = res.data;
						console.log(that.CommentsList)
					},
					fail(err) {
						console.log(err);
					}
				});
			}
		}
	}
</script>

<style>
	/* 问题列表 */
	.que-list-center{
		margin-left: 44upx;
		margin-top: 10upx;
	}
	.que-list-name-color{
		color: #3294ff;
		margin-right: 10upx;
	}
	.que-list-name{
		margin-left: 14upx;
		font-size: 28upx;
		color: #969696;
	}
	.que-list-q{
		height: 34upx;
		width: 34upx;
		overflow: hidden;
	}
	.que-list-view{
		display: flex;
		justify-content: flex-start;
		align-items: center;
	}
	.que-center{
		margin: 30upx;
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
	
	/* 提交问题 */
	.que-post-view{
		background-color: #FFFFFF;
		border-radius: 20upx;
		padding: 30upx;
		margin: 30upx;
		display: flex;
		justify-content: start;
		align-items: center;
	}
	/* 全局 */
	.fengrui-img {
		height: 100%;
		width: 100%;
	}
	
	page {
		background-color: #F7F7F7;
	}
</style>
