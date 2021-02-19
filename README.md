## 概述
实人认证，确认是本人操作（无感活体）并比对身份信息与本人是否匹配，完整的介入文档可参考：[https://esandinfo.yuque.com/books/share/5639442d-125b-46c8-b7fa-845c5c8649ea?#](https://esandinfo.yuque.com/books/share/5639442d-125b-46c8-b7fa-845c5c8649ea?#),如下

---

![demovideo](http://open.esandcloud.com/share/index.php/s/zSk9W8AlZqY2W8O/download)

## 插件接口说明
插件地址：[https://ext.dcloud.net.cn/plugin?id=2357](https://ext.dcloud.net.cn/plugin?id=2357)

接口如下
### 初始化
```java
/**
 * 认证初始化
 * @param options(JSONObject), 包括如下字段：
     mCertName：姓名
     mCertNo：中国大陆身份证号
     businessId：业务ID(可为空，将会在getResult接口返回)
 * @return 响应结果格式如下
	{
		"success": true, -- 执行是否成功，执行成功data字段才有数据
		"message": "执行成功", -- 结果描述
		"code": "ZOLOZ_SUCCESS", -- 状态码
		"data": "" -- 执行结果
	}
  code：执行结果的状态码
    ZOLOZ_SUCCESS：执行成功
    ZOLOZ_FAILED：执行失败
    ZOLOZ_UNKNOW_ERR：未知错误
    ZOLOZ_CLIENT_ERROR：本地代码执行抛异常
    ZOLOZ_SYSTEM_ERROR：系统错误
    ZOLOZ_CANCEL：执行流程已经被用户主动取消
    ZOLOZ_NETWORK_ERROR：网络异常
    ZOLOZ_SERVER_ERROR：服务器端返回的数据异常
 */
 ZolozModule.authInit(options);
 ```

 ### 发起实人认证
 ```java
/*
 * 执行实人认证（完成后请到https://ediszim.market.alicloudapi.com/zoloz/zim/getResult获取最终的认证结果）
 * @param options(JSONObject), 包括如下字段：
     initMsg：认证初始化服务器端返回的数据
 * @param callback 执行结果回调，传入参数为对象result，result包括如下几个字段：
    {
        "success":true,
        "message":"{"bizid":"20201019173432663b87e5cd1c5eb45e","certifyId":"3d8ae79ab266cb92809e35c52d26d095"}", -- 执行结果，未必是json字符串
        "code":"ZOLOZ_SUCCESS",
        "data":"{"bizid":"20201019173432663b87e5cd1c5eb45e","certifyId":"3d8ae79ab266cb92809e35c52d26d095"}" -- 执行结果，为空后者json字符串
    }
  code：执行结果的状态码
    ZOLOZ_SUCCESS：执行成功
    ZOLOZ_FAILED：执行失败
    ZOLOZ_UNKNOW_ERR：未知错误
    ZOLOZ_CLIENT_ERROR：本地代码执行抛异常
    ZOLOZ_SYSTEM_ERROR：系统错误
    ZOLOZ_CANCEL：执行流程已经被用户主动取消
    ZOLOZ_NETWORK_ERROR：网络异常
    ZOLOZ_SERVER_ERROR：服务器端返回的数据异常
 *
 */
ZolozModule.zolozAuth(options, callback);
```

## 其他信息
1. 完整接入文档：[https://esandinfo.yuque.com/books/share/5639442d-125b-46c8-b7fa-845c5c8649ea?#](https://esandinfo.yuque.com/books/share/5639442d-125b-46c8-b7fa-845c5c8649ea?#)
2. 服务器端协议文档：[https://market.aliyun.com/products/57000002/cmapi00041091.html#sku=yuncode3509100001](https://market.aliyun.com/products/57000002/cmapi00041091.html#sku=yuncode3509100001)
3. 后端管理控制台地址: [http://openali.esandcloud.com](http://openali.esandcloud.com)
4. 技术支持
```
微信：esand_info
qq: 3626921591
电话：18033076802
邮箱：ruide.li@esandinfo.com
```
![wechatqrcode](http://open.esandcloud.com/share/index.php/s/hzT4Gb0BN81svae/download)
