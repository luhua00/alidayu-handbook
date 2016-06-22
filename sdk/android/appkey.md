# AppKey防盗

## 操作步骤

版本1.1.0以后，短信SDK通过本地代码，强化了appkey的防盗用功能。下面是具体的使用步骤：

1. 参考短信SDK集成文档将最新版本的SDK集成到您的项目中
2. 对您的应用进行正式包签名并安装到您的测试机中
3. 参考下图，将SMSSDKSignCal.apk安装到您的测试机中并启动

![appkey secret](http://demo.mob.com/wiki/wp-content/uploads/2014/11/smssmdk_appkey_3.png)

4. 在输入框中填写您应用的包名，然后点击“计算”，会得到您应用的签名指纹

![smssmdk_appkey_cal](http://demo.mob.com/wiki/wp-content/uploads/2014/11/smssmdk_appkey_2.png)

5. 登录您的“短信验证组件”管理后台，展开左侧的“应用管理”，选择“应用信息”，在右侧的“应用签名”里添加SMSSDKSignCal计算得到的签名指纹，并保存配置

![smssmdk_appkey_3](http://demo.mob.com/wiki/wp-content/uploads/2014/11/smssmdk_appkey_3.png)

## 注意事项


> 1、完成上述步骤以后，您的应用若要对短信SDK进行操作，则一定要对其进行签名，否则短信SDK的服务器将拒绝任何请求。
> 2、应用后台本身允许您添加多个签名，因此您可以将您的测试签名和正式签名都填写到应用后台
> 3、如果您从未在应用后台填写您的签名，则短信SDK服务器默认不会对您应用的签名值进行检查，所以任何签名的请求都会被接受
> 4、请妥善保存您的应用签名，避免泄漏
