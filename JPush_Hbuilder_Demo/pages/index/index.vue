<template>
    <div>
		</br>
		</br>
		<label style="margin-right: 50rpx;">网络状态:</label>
		<label>{{connectStatus}}</label>
		</br>
		<label style="margin-right: 50rpx;">RegistrationID:</label>
		<label>{{registrationID}}</label>
		</br>
		</br>
		
		<label>---------------------------------</label>
		<button type="primary" @click="openSettingsForNotification">打开通知设置界面</button>
		</br>
		<button type="primary" @click="setLoggerEnable">打开日志</button>
		</br>
		<button type="primary" @click="setLoggerUnEnable">关闭日志</button>
		</br>
		<button type="primary" @click="getRegistrationID">获取注册id</button>
		
    </div>
</template>

<script>
    // 首先需要通过 uni.requireNativePlugin("ModuleName") 获取 module 
    var jpushModule = uni.requireNativePlugin("JG-JPush")
    export default {
		
		data() {
			return {
				connectStatus: '未链接',
				registrationID: '未获得',
			}
		},
		
		onShow() {
			if(uni.getSystemInfoSync().platform == "ios"){
				// iOS端使用应用内消息需要在页面进入和离开的时候配置pageEnterTo和pageLeave
				jpushModule.pageEnterTo("HomePage");
			}
		},
		
		onHide() {
			if(uni.getSystemInfoSync().platform == "ios"){
				jpushModule.pageLeave("HomePage");
			}
		},
		
		onLoad() {
			console.log('开始监听连接状态')
			uni.$on('connectStatusChange',(connectStatus)=>{  
				   var connectStr = ''
				   if (connectStatus == true) {
					   connectStr = '已连接'
					   this.getRegistrationID()
				   }else {
					   connectStr = '未连接'
				   }
				   console.log('监听到了连接状态变化 --- ', connectStr) 
				   this.connectStatus = connectStr
			    })  
		},
		
		onUnload() {  
			// 移除监听事件  
		    uni.$off('connectStatusChange')
		},
		
        methods: {
			
			openSettingsForNotification() {
				jpushModule.openSettingsForNotification((result)=>{
					this.showToast(result)
				})
			},
			
			setLoggerEnable() {
				jpushModule.setLoggerEnable(true)
			},
			
			setLoggerUnEnable() {
				jpushModule.setLoggerEnable(false)
			},
			
			getRegistrationID() {
				jpushModule.getRegistrationID(result=>{
					let registerID = result.registerID
					console.log(registerID)
					this.registrationID = registerID
				})	
			},
			
			showToast(result){
				uni.showToast({
					icon:'none',
					title: JSON.stringify(result),
					duration: 3000
				})
			}

        }
    }
</script>
