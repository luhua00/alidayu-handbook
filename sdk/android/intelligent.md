# 智能验证

## 1、功能说明

从Android 1.3.1版本开始，我们新增了智能验证功能。智能验证是指您的手机号最近一次已经在Mob平台的SMSSDK验证过。此时就不再下发短信到该手机上，直接通过验证。当用户手机未通过智能验证时，则依然需要下发短信/语音验证码的方式验证手机号的有效性。

如果你不想使用此功能，可以在短信的后台关闭，默认是打开

## 2、SDK中如何使用

智能验证依然在获取短信验证的api（SMSSDK.getVerificationCode）里，只是操作回调中，需要判断一下。当result=SMSSDK.RESULT_COMPLETE，则data的类型为Boolean，你需要判断一下，如果为true，便是智能验证。

```java
if (event == SMSSDK.EVENT_GET_VERIFICATION_CODE) {
    if(result == SMSSDK.RESULT_COMPLETE) {
	boolean smart = (Boolean)data;
	if(smart) {
           //通过智能验证
        } else {
           //依然走短信验证
        }
     }
}
```
