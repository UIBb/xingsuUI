<template>
	<view>
		<web-view :src="weatl"></web-view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				weatl:''
			}
		},
		onLoad(e) {
			
			this.weatl = e.aul
			console.log(this.weatl)
		},
		methods: {
			
		}
	}
</script>

<style>

</style>
