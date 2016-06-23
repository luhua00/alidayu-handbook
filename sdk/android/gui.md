# 无GUI接口调用

## 1. 无GUI接口概述
- [github demo](https://github.com/MobClub/SMSSDK-for-Android)
- [旧版SMSDemo](http://wiki.mob.com/wp-content/uploads/2014/11/SMSDemo.zip)

本文只介绍短信SDK内置（无界面）方法的使用和注意事项，如果您想查阅有界面的操作方法，请参考“短信SDK集成文档”相关章节的说明。

尽管短信SDK提供了简化集成步骤的GUI功能，但它同样允许开发者放弃这套GUI，直接与核心代码进行交互。

> * Studio: MobCommons-xxx.jar,MobTools-xxx.jar，SMSSDK-<版本号>.aar
> * Eclipse:短信SDK的核心代码存放于 SMSSDK/libs中的“SMSSDK-<版本号>.jar”,集成的时候除了将SMSSDK-<版本号>.jar复制到自己的工程libs下，还要复制MobCommons-xxx.jar,MobTools-xxx.jar和资源文件,还有SMSSDK的res下的raw要复制到你的工程的res下；如果版本小于2.0.1，不用复制raw，另外要复制libsmssdk.so。

其中对外主要的类有：SMSSDK和EventHandler，所有的操 作都通过SMSSDK来发起，并通过EventHandler来接收。由于EventHandler已有SMSSDK操作回调章节讲述，因此本节之着重介绍SMSSDK的各个方法。

## 2. 无GUI接口说明
### 1. 初始化接口
  
<table>
<tbody>
<tr>
<th>限定符和类型</th>
<th>方法和说明</th>
</tr>
<tr>
<td>static void</td>
<td>initSDK(Context context, String appkey, String appSecrect)<br>
初始化SDK,单例，可以多次调用；任何方法调用前，必须先初始化</td>
</tr>
<tr>
<td>static void</td>
<td>registerEventHandler(EventHandler handler)<br>
注册回调接口</td>
</tr>
<tr>
<td>static void</td>
<td>unregisterEventHandler(EventHandler handler)<br>
注销回调接口</td>
</tr>
</tbody>
</table>

* **initSDK**方法是短信SDK的入口，需要传递您从ShareSDK应用管理后台中注册的应用**AppKey**和**AppSecrete**，如果填写错误，后续的操作都将不能进行。
* **registerEventHandler**用来往SMSSDK中注册一个事件接收器，SMSSDK允许开发者注册任意数量的接收器，所有接收器都会在事件 被触发时收到消息。

```java
SMSSDK.initSDK(this,APPKEY,APPSECRET);
		EventHandler eh=new EventHandler(){

			@Override
			public void afterEvent(int event, int result, Object data) {

			   if (result == SMSSDK.RESULT_COMPLETE) {
				//回调完成
				if (event == SMSSDK.EVENT_SUBMIT_VERIFICATION_CODE) {
                //提交验证码成功
				}else if (event == SMSSDK.EVENT_GET_VERIFICATION_CODE){
			    //获取验证码成功
				}else if (event ==SMSSDK.EVENT_GET_SUPPORTED_COUNTRIES){
                //返回支持发送验证码的国家列表
                } 
              }else{                                                                 
                 ((Throwable)data).printStackTrace(); 
          }
      } 
   }; 
```
- **registerEventHandler**必须和**unregisterEventHandler**配套使用，否则可能造成内存泄漏。
### 2. 短信验证码接口

<table>
<tbody>
<tr>
<th>限定符和类型</th>
<th>方法和说明</th>
</tr>
<tr>
<td>static void</td>
<td>getSupportedCountries()<br>
获取短信目前支持的国家列表，在监听中返回</td>
</tr>
<tr>
<td>static void</td>
<td>getVerificationCode(String country, String phone)<br>
请求获取短信验证码，在监听中返回</td>
</tr>
<tr>
<td>static void</td>
<td>submitVerificationCode(String country, String phone, String code)<br>
提交短信验证码，在监听中返回</td>
</tr>
</tbody>
</table>

* 短信SDK并不能支持世界上所有国家的短信验证服务，因此我们提供了getSupportedCountries方法，在使用短信验证码功能前请调用此方法，获取当前SDK可以支持的国家列表和号码匹配规则。
* getVerificationCode用于向服务器请求发送验证码的服务，需要传递国家代号和接收验证码的手机号码，支持此服务的国家代码在 getSupportedCountries中获取。请求getVerificationCode的时间间隔不应该小于60秒，否则服务端会返回“操作过 于频繁”的错误
* submitVerificationCode用于向服务器提交接收到的短信验证码，验证成功后会通过EventHandler返回国家代码和电话号码。

### 3. 用户接口

<table>
<tbody>
<tr>
<th>限定符和类型</th>
<th>方法和说明</th>
</tr>
<tr>
<td>static void</td>
<td>submitUserInfo(String uid, String nickname, String avatar, String country, String phone)<br>
提交用户信息，在监听中返回</td>
</tr>
<tr>
<td>static void</td>
<td>getNewFriendsCount()<br>
获取应用内新增加的好友数，在监听中返回</td>
</tr>
<tr>
<td>static void</td>
<td>getFriendsInApp()<br>
获取应用内的好友列表，在监听中返回</td>
</tr>
</tbody>
</table>

* 短信SDK的短信验证码功能可以当作是一个“第三方登录”的功能，通过检验验证码可以得到手机号码的准确性，确认了用户身份以后就可以执行登录和注 册了。注册用户以后，开发者可以通过调用submitUserInfo方法向我们的服务器提交用户资料，提交的资料将当作“通信录好友”功能的建议资料。
* 提交了用户资料以后即可以通过调用getNewFriendsCount获取当前设备通信录于您应用内的用户数量。并且可以通过getFriendsInApp获取本设备通信录于当前应用内的用户列表。

### 4. 语音验证码接口

<table>
<tbody>
<tr>
<th>限定符和类型</th>
<th>方法和说明</th>
</tr>
<tr>
<td>static void</td>
<td>2.0.0以下版本第一个参数为手机号。&nbsp;<p></p>
<p>getVoiceVerifyCode(String phone , String country)</p>
<p>为了与发送文本保持一致。2.0.0与之后的改成第一个为国家代码。</p>
<p>getVoiceVerifyCode(String country，String phone)<br>
请求语音验证码，在监听中返回</p></td>
</tr>
</tbody>
</table>

* getVoiceVerifyCode方法主要用于在短信接收比较慢的情况下的第二种选择，需要传递国家代号和接收验证码的手机号码，语音验证码将会以电话语音的方式获取。
