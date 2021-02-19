<template>
  <view>
    <form>
      <view align="center">
        <image class='esand_logo' src="../../static/logo.png"></image>
      </view>
      <view class="infos">
        <view class="title">姓名:</view>
        <input class="uni-input" name="mCertName" placeholder="请输入姓名"  v-model="mCertName"/>
        <view class="title">身份证号:</view>
        <input class="uni-input" type="idcard" name="mCertNo" placeholder="请输入身份证号" v-model="mCertNo"/>
      </view>
      <view align="center">
		<button class="btSubmit" @click="formSubmit()">实人认证</button>
      </view>
    </form>
	<div align="center">
		<textarea v-model="msg" />
	</div>
	<!-- <div align="center">
		<textarea :value="msg" />
	</div> -->
  </view>
</template>

<script>
  // TODO 替换成您的APPCODE
  var APPCODE = "2294b96c1dce4aedafa39bfa3fc5708c";
  var ZolozModule = uni.requireNativePlugin("Esand-ZolozModule");
  import eButton from "../../components/eButton.vue";
  export default {
	components:{
		eButton
	},
    data() {
      return {
        msg:"logs",
		mCertName:"",
		mCertNo:""
      }
    },
    methods: {
      formSubmit: function(e) {
		let _this=this;
        console.log('form发生了submit事件，携带数据为：' +JSON.stringify(_this.mCertName,_this.mCertNo));
        // 认证初始化, 详细的请求协议可参考：https://market.aliyun.com/products/57000002/cmapi00041091.html
        var zolozResult = ZolozModule.authInit({
            'mCertNo': _this.mCertNo,
            'mCertName': _this.mCertName
          });
        console.log("zolozResult: ", zolozResult)
        if (zolozResult.code=='ZOLOZ_SUCCESS') {
          var initData = JSON.parse(zolozResult.data)
          // 认证初始化，获取认证授权(为了保护APPCODE, 此段逻辑建议放在服务器端)
          console.log("bizId:" + initData.bizId)
          uni.request({
            url: 'https://ediszim.market.alicloudapi.com/zoloz/zim/init',
            method: 'POST',
            data: {
			  //业务id(2.0服务端生成，不作为业务关联使用)
              "bizId": initData.bizId,
			  //客户业务标识（可用于交易关联，在getResult返回）
              "businessId": initData.bizId,
			  //客户端采用的RSA加密传输的身份信息Base64编码字符串
              "identityParam": initData.identityParam,
			  //metainfo 环境参数，需要通过客户端 SDK 获取
              "metaInfo": initData.metaInfo,
			  //应用名称
              "appName": initData.appName,
			  //APP签名
              "appSign": initData.appSign,
			  //包名
              "packageName": initData.packageName,
			  //平台：android,ios
              "platform": initData.platform
            },
            header: {
              'Authorization': 'APPCODE ' + APPCODE,
              'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8'
            },
            success: (res) => {
              console.log('网络请求成功' + JSON.stringify(res))
              // 发起认证操作
              ZolozModule.zolozAuth({"initMsg": res.data},
                (ret) => {
                  console.log(JSON.stringify(ret))
                  if (ret.code=='ZOLOZ_SUCCESS') {
                    var reqJson = JSON.parse(ret.data);
                    var bizId = reqJson.bizId
                    var certifyId = reqJson.certifyId
                    // 获取认证结果（认证完成计费，并不是获取结果时候计费，所以为了节省成本，请保证调取到获取结果） (为了保护APPCODE, 此段逻辑建议放在服务器端)
                    console.info("hello certifyId = " + certifyId)
                    console.info("hello bizid = " + bizId)
                    uni.request({
                      url: 'https://ediszim.market.alicloudapi.com/zoloz/zim/getResult',
                      method: 'POST',
                      data: {
                        'bizId': bizId,
                        'certifyId': certifyId
                      },
                      header: {
                        'Authorization': 'APPCODE ' + APPCODE,
                        'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8'
                      },
                      success: (res) => {
                        console.log('网络请求成功' + JSON.stringify(res.data));
						_this.msg+="获取结果成功"+JSON.stringify(res.data);
                      }
                    })
                  } else {
					_this.msg+="发生错误"+JSON.stringify(ret);
                  }
                })
            }
          })
        } else {
			_this.msg+="发生错误"+JSON.stringify(zolozResult);
        }
      },
    }
  }
</script>

<style>
	
.esand_logo {
  margin-top: 100rpx;
  width: 200rpx;
  height: 200rpx;
  align-self: center;
}

.btSubmit {
  margin-top: 300rpx;
  margin: 100rpx 150rpx 0rpx 100rpx;
}

.infos {
  margin: 100rpx 80rpx 0rpx 100rpx;
}

.title {
  margin-bottom: 15rpx;
  margin-top: 10rpx;
}

input {
  margin-top: 20rpx;
  margin-bottom: 30rpx;
}
</style>