<template>
  <view>
    <form @submit="formSubmit">
      <view align="center">
        <image class='esand_logo' src="../../static/logo.png"></image>
      </view>
      <view class="infos">
        <view class="title">姓名:</view>
        <input class="uni-input" name="mCertName" placeholder="请输入姓名" value="" />

        <view class="title">身份证号:</view>
        <input class="uni-input" type="idcard" name="mCertNo" placeholder="请输入身份证号" value=""/>
      </view>
      
      <view align="center">
        <button class="btSubmit" form-type="submit">实人认证</button>
      </view>
    </form>
  </view>
</template>

<script>
  // TODO 替换成您的APPCODE
  var APPCODE = "您的APPCODE";
  var ZolozModule = uni.requireNativePlugin("Esand-ZolozModule");
  export default {
    data() {
      return {
        
      }
    },
    methods: {
      formSubmit: function(e) {
        console.log('form发生了submit事件，携带数据为：' +JSON.stringify(e.detail.value))
        // 认证初始化, 详细的请求协议可参考：https://market.aliyun.com/products/57000002/cmapi00041091.html 
        var zolozResult = ZolozModule.authInit({
            'mCertNo': e.detail.value.mCertNo,
            'mCertName': e.detail.value.mCertName
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
              "bizId": initData.bizId,
              "businessId": initData.bizId,
              "identityParam": initData.identityParam,
              "metaInfo": initData.metaInfo,
              "appName": initData.appName,
              "appSign": initData.appSign,
              "packageName": initData.packageName,
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
                        console.log('网络请求成功' + JSON.stringify(res.data))
                        uni.showModal({
                          title: "获取结果成功",
                          content: JSON.stringify(res.data),
                        })
                      }
                    })
                  } else {
                    uni.showModal({
                      title: "发生错误",
                      content: JSON.stringify(ret),
                    })
                  }
                })
            }
          })
        } else {
          uni.showModal({title: "发生错误",content: JSON.stringify(zolozResult),})
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