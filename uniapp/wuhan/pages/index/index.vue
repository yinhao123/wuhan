<template>
	<view>
		<!-- 头部内容 -->
	
			<!-- 内容介绍 -->
			<view class="flex padding">
				<view class="flex-sub text-xxl text-bold text-center padding-sm margin-xs radius">同程信息查询</view>
				<view  class="flex-sub text-xxl text-blue text-center align-center padding-sm margin-xs radius ">
					<button class="cu-btn block line-blue " type="primary" size="mini" plain="true" open-type="share"><text class="cuIcon-upload"></text>分享给好友</button>
					</view>
			</view>
		
		<!-- 中间提交信息的内容 -->
		<view class="padding">
			<form >
				
				<view class="cu-form-group">
					<view class="title">出发</view>
					<input placeholder="请输入乘车地点,例如:武汉" v-model="start_pos"  name="input"></input>
				</view>
				<view class="cu-form-group">
					<view class="title">到达</view>
					<input placeholder="请输入乘车地点,例如:武汉" v-model="end_pos"  name="input"></input>
				</view>
				<view class="cu-form-group">
					<view class="title">车次</view>
					<input placeholder="请输入车次信息,例如:G1234" :value="trainNumber" name="input"></input>
				</view>
				<view class="cu-form-group">
					<view class="title">日期</view>
					<picker mode="date" :value="date" start="2019-10-01" end="2020-10-01" @change="DateChange">
						<view class="picker">
							{{date}}
						</view>
					</picker>
				</view>
				<view class="padding flex flex-direction">
					<button class="cu-btn bg-blue lg" @click="queryInfo">查询</button>
				</view>
			</form>
		</view>
		<!-- 搜索结果内容 -->
		<view>
			<view v-if="noResult" class="cuIcon-safe text-xxl text-center  text-green">
				无该车次信息！
			</view>
			<view v-else class="">
				<view class="padding ">
					<view  v-for="(item, index) of trainList" :key="index" class="flex  p-xs margin-bottom-sm mb-sm" @click="navToDetail(item.id)"  >
						<view class="flex-sub solid text-center bg-cyan padding-sm margin-xs radius">
							<!-- 图标 -->
								<!-- <image v-if="item.type = 1" src="../../static/image/plane.png" mode="aspectFit" class="tran-style"></image>
								<image v-else-if="item.type = 2" src="../../static/image/train.png" mode="aspectFit" class="tran-style"></image>
								 <image v-else="item.type = 3" src="../../static/image/bus.png" mode="aspectFit" class="tran-style"></image> -->
							<!-- <view v-if="item.type = 1" class="">
								{{item.type}}
								<image src="../../static/image/plane.png" mode="aspectFit" class="tran-style"></image>
							</view>
							<view v-else class="">
								{{item.type}}
								<image src="../../static/image/bus.png" mode="aspectFit" class="tran-style"></image>
							</view> -->
							
							<!-- <view v-if="item.type = 1" class="">
								飞机 {{item.type}}{{item.id}}
							</view>
							<view v-else-if="item.type = 2" class="">
								火车
							</view>
							<view v-else class="">
								其他
							</view> -->
							<view class="">
								车次信息
							</view>
							<view>
								{{item.no}}
							</view>
						</view>
						
						<view class="flex-treble bg-gradual-blue padding-sm margin-xs radius">
							<view class="">
								日期：{{item.date}}
							</view>
							<view class="">
								车次：{{item.no}}
							</view>
							<view class="">
								{{item.strat_pos?item.strat_pos:''}}   <text class="cuIcon-right"></text>  {{item.end_pos}}
							</view>
							<view class="" >
								点击查看详情
							</view>
							</view>
						
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	
	// import  getTrainInfo  from '../../common/request'
	export default {
		// components:{
		// 	getTrainInfo
		// },
		data() {
			return {
						date: '2020-2-1',
						trainNumber: null,
						start_pos: null,
						end_pos:null,
						trainList:'',
						config:{},
						noResult:false
						
			}
		},
		onLoad() {
			this.getTrain();
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
			  getTrainInfo() { // 参数：用户名
				return this.post('/all', {
				})
			},
			
			async getTrain(){
			 let data =	await this.getTrainInfo();
			 console.log("run...");
			 console.log(data);
			 console.log(data.data.code);
			 if (data.data.code == 200) {
			 	this.trainList = data.data.data;
				
				uni.setStorage({
				    key: 'trainList',
				    data: this.trainList,
				    success: function () {
				        console.log('success');
				    }
				});
			 
			 }
			},
			shareYourFriend(){
				// 分享给好友
				uni.showShareMenu();
			},
			queryInfos(){
			
				let date = this.date;
				let no = this.trainNumber;
				let strat_pos = this.start_pos;
				let end_pos = this.end_pos;
				return this.post('/condition', {
					date,
					no,
					strat_pos,
					end_pos
				})
			},
			async queryInfo(){
			 let data =	await this.queryInfos();
			 
			 if (data.data.code == 200) {
				
				if(data.data.data.length == 0){
					this.noResult = true;
				}
			 	this.trainList = data.data.data;
				uni.setStorage({
				    key: 'trainList',
				    data: this.trainList,
				    success: function () {
				        console.log('success');
				    }
				});
			 
			 }
			},
			DateChange(e) {
				this.date = e.detail.value
			},
			navToDetail(e){
				console.log(e);
				
				uni.navigateTo({
					url:'../detail/detail?id='+e
				})
			}
		}
	}
</script>

<style>
.tran-style{
	width: 100%;
	height: 30%;
}
</style>
