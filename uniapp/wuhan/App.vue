<script>
    export default {
        onLaunch: function() {
            console.log('App Launch');
            // #ifdef APP-PLUS
            // 检测升级
            uni.request({
                url: 'https://uniapp.dcloud.io/update', //检查更新的服务器地址
                data: {
                    appid: plus.runtime.appid,
                    version: plus.runtime.version,
                    imei: plus.device.imei
                },
                success: (res) => {
                    if (res.statusCode == 200 && res.data.isUpdate) {
                        let openUrl = plus.os.name === 'iOS' ? res.data.iOS : res.data.Android;
                        // 提醒用户更新
                        uni.showModal({
                            title: '更新提示',
                            content: res.data.note ? res.data.note : '是否选择更新',
                            success: (showResult) => {
                                if (showResult.confirm) {
                                    plus.runtime.openURL(openUrl);
                                }
                            }
                        })
                    }
                }
            })
            // #endif
			
        },
        onShow: function() {
			
				uni.setNavigationBarTitle({
				    title: '新型肺炎确诊患者同程查询'
				});
			
            console.log('App Show')
        },
        onHide: function() {
            console.log('App Hide')
        },
		globalData: {
			test: '',
			hosturl:'http://localhost:8080/api'
		},
		methods:{
			async  post(url, data) {
				return new Promise(function(resolve, reject) {
					uni.request({
						url: 'http://localhost:8080/api' + url,
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
		}
    }
</script>

<style>
	@import "colorui/main.css";
	@import "colorui/icon.css";
	
    /* #ifndef APP-PLUS-NVUE */
    /* uni.css - 通用组件、模板样式库，可以当作一套ui库应用 */
    @import './common/uni.css';

    /* 以下样式用于 hello uni-app 演示所需 */
    page {
        background-color: #F4F5F6;
        height: 100%;
        font-size: 28upx;
        line-height: 1.8;
    }

    .uni-header-logo {
        padding: 30upx;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        margin-top: 10upx;
    }

    .uni-header-image {
        width: 100px;
        height: 100px;
    }

    .uni-hello-text {
        color: #7A7E83;
    }

    .uni-hello-addfile {
        text-align: center;
        line-height: 300upx;
        background: #FFF;
        padding: 50upx;
        margin-top: 10px;
        font-size: 38upx;
        color: #808080;
    }

    /* #endif*/
</style>
