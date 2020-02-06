<template>
	<view class="padding">
		<!-- 头部信息 -->
		<view class=" bg-gradual-blue radius padding flex  p-xs margin-bottom-sm mb-sm">
				<view class="flex-twice text-center padding-sm margin-xs radius">
					<view class="">
						交通类型
					</view> 
					<view class="text-xxl text-bold">
						飞机{{trainDetail.type}}
					</view>
				</view>
				<view class="flex-twice text-center padding-sm margin-xs radius">
					<view class="">
						车次信息
					</view> 
					<view class="text-red text-bold text-xxl">
						{{trainDetail.no}}
					</view>
				</view>
				<view class="flex-twice  padding-sm margin-xs radius">
						<image class="tran-style" src="../../static/image/plane.png" mode="aspectFit"></image>
				</view>
		</view>
		<view class="">
				<view class="">
					车次时间：{{trainDetail.date}}
				</view>
				<view>
					经过地点：{{item.start_pos?item.start_pos:''}} {{trainDetail.end_pos}}
				</view>
				<view>
					其他信息：{{trainDetail.memo}}
				</view>
				<view class="">
					信息来源：{{trainDetail.from}} 
				</view>
		</view>
	</view>
</template>

<script>
	// import { getTrainDetail } from '@/common/request.js'
	export default {
		// components:{
		// 	getTrainDetail
		// },
		data() {
			return {
				trainNo:1,
				trainDetail:''
			}
		},
		onLoad(options) {
			console.log("传过来的值");
			console.log(options.id);
			this.trainNo = options.id;
			uni.setNavigationBarTitle({
			    title: '详情信息'
			});
			this.getTrainDetails(options.id);
		},
		
		methods: {
			async  post(url, data) {
				return new Promise(function(resolve, reject) {
					uni.request({
						url: getApp().globalData.hosturl + url,
						method: 'POST',
						header: {
							"content-type": 'application/x-www-form-urlencoded'
						},
						data,
						success: function(data) {
							resolve(data)
						},
						fail: function(err) {
							// 回调失败时
							console.log(err)
							if (typeof reject == 'function') {
								reject(data);
							} else {
								console.log(data);
							}
						}
					})
				})
			},
			getTrainDetail(id) { // 参数：id
				return this.post('/id', {
					id
				})
			},
			async getTrainDetails(id){
			 let data =	await this.getTrainDetail(id);
			 console.log("run...");
			 console.log(data);
			 console.log(data.data.code);
			 if (data.data.code == 200) {
				
			 	this.trainDetail = data.data.data;
				console.log(data.data)
			 
			 }
			},
		}
	}
</script>

<style>
.tran-style{
	width: 100%;
	height: 100%;
}
</style>
