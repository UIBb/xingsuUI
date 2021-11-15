<!--/*
 * 
 * 星宿ui设计
 * author: yinfengrui
 * organization: 枫瑞博客  www.frbkw.com
 * 微信公众号:   枫瑞博客网
 * 技术支持微信号：qaz449275996
 * 开源协议：MIT
 *Copyright (c) 2020 https://www.frbkw.com All rights reserved.
 */-->


<script>
	const updateManager = uni.getUpdateManager();
	export default {
		globalData:{
			totalte_sources:[],
			togetrecdown:[]
		},
		onLaunch: function() {
			console.log('App Launch');
			// 检测更新
			this.Getup();
		},
		onShow: function() {
			console.log('App Show');
		},
		onHide: function() {
			console.log('App Hide');
		},
		onThemeChange: function(options) {
			console.log(options, '系统主题');
		},
		methods: {
			// 检测更新
			Getup: function() {
				updateManager.onCheckForUpdate(function(res) {
					// 请求完新版本信息的回调
					console.log(res.hasUpdate);
				});
				updateManager.onUpdateReady(function(res) {
					uni.showModal({
						title: '更新提示',
						content: '新版本已经准备好，是否重启应用？',
						success(res) {
							if (res.confirm) {
								// 新的版本已经下载好，调用 applyUpdate 应用新版本并重启
								updateManager.applyUpdate();
							}
						}
					});
				});
				updateManager.onUpdateFailed(function(res) {
					// 新的版本下载失败
				});
			},
		}
	};
</script>

<style>
	/* 解决头条小程序组件内引入字体不生效的问题 */
	@import url("/components/gaoyia-parse/parse.css");

	/* #ifdef MP-TOUTIAO */
	@font-face {
		font-family: uniicons;
		src: url('/static/uni.ttf');
	}

	/* #endif */
</style>
