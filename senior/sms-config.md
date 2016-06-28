---
date: 2016-06-27 14:28
status: public
title: '短信后台控制信息 Setting'
---

# 短信后台控制信息 Setting

## 页面地址（http://dashboard.mob.com/#/sms/config/setting）
 - 登录Mob官网后台，点击[短信设置][1]进入页面

![短信签名设置][2]

## 签名设置
 - 如果开发者没有申请自定义签名，发送的短信验证码内容则为：
 
 >**中国大陆：** *ios_sms*的验证码是：4587【掌淘科技】
 >**非中国大陆：**
 Your pin is 4587. Use this pin for App Mob.
 上例中的斜体字：ios_sms是开发者集成SMSSDK的应用名称。
 - 如果开发者打开**全球设置**，**港澳台设置**下的开关。如果应用名称中包含中文，请务必设置对应的英文名称。否则，发送非中国大陆以外的地区短信会失败.
        如果应用名称已经是英文，则可设可不设，如果设置，发送非中国大陆以外地区的验证码短信中的应用名称将使用新设置的名称。

## 1. 中国大陆签名
 - 国内短信修改签名需要提供公司营业执照等资料给运营商审核通过才能生效。
 - 点击进入自定义短信签名页面设置（http://dashboard.mob.com/#/sms/config/customsign）

![自定义签名设置][3]
 - 点击右上角“申请签名”按钮

![签名信息提交][4]
 - 列表项

## 签名信息规则
- 短信签名：必填 3-8位字符，不可全是英文字符，不可包含特殊符号，比如下划线、[] () 等。
- 公司名称：必填 1-100位字符
- 公司营业执照：必填
- 证明截图：不必填
- 短信签名与公司名称不符，比如改成了APP名称，则需提供APP的商标证明文件或软件著作权或APP在APPStore等应用市场上的上线证明截图（需有公司名称在上面）

## 签名同步
- 旧版（2.X之前的版本）已申请过签名的应用，新版本（2.X之后的版本）无需重新申请自定义签名，可以使用同个签名。
- 原应用的AppKey只能转移一次

## 2. 中国港澳台签名
- 签名只包含英文和数字，不可为特殊字符，不可包含中文字符

![](http://wiki.mob.com/md/images/sms-config-3.png)

## 3. 全球签名
- 签名只包含英文和数字，不可为特殊字符，不可包含中文字符
- 北美地区不支持


![](~/全球签名设置.png)

## 服务器验证
- 通过开发者的服务器进行验证，[文档](http://sms.wiki.mob.com/sdk/android/server.html)

![](http://wiki.mob.com/md/images/sms-config-4.png)

## HTTP API接口
- 绑定服务器IP, IP数不能大于10个
- IP地址间使用英文逗号(,)分隔
- 输入完IP，请按回车键。
- 未开启此开关，不可创建短信模板
- 需要使用Web-api发送验证码短信，校验验证码，需要使用自定义短信接口，必须开启此开关并设置IP列表。

![](http://wiki.mob.com/md/images/sms-config-5.png)

## 智能验证
- 开启后，若该设备待验证手机号码、手机设备唯一ID和SIM卡序列号，与上一次该设备验证通过的手机号码、手机设备唯一ID和SIM卡序列号完全一致。则无需进行短信验证，直接智能验证通过。（仅针对安卓设备、使用短信验证码SDKV1.3.1以上版本有效）

![](http://wiki.mob.com/md/images/sms-config-6.png)



  [1]: http://dashboard.mob.com/#/sms/config/setting
  [2]: http://wiki.mob.com/md/images/sms-set-1.png
  [3]: http://wiki.mob.com/md/images/sms-set-2.png
  [4]: http://wiki.mob.com/md/images/sms-set-3.png