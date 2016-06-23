# SDK回调操作

注册回调：

```java
SMSSDK.registerEventHandler(EventHandler);
```

其中的EventHandler即为操作回调。它包括4个方法，分别为：

```java
public void onRegister();
public void beforeEvent(int event, Object data);
public void afterEvent(int event, int result, Object data);
public void onUnregister();
```

其中onRegister在回调对象注册的时候被触发。beforeEvent在操作执行前被触发，其参数event表示操作的类型，data是从外部传入的数据。afterEvent在操作结束时被触发，同样具备event和data参数，但是data是事件操作结果，其具体取值根据参数result而定。result是操作结果，为SMSSDK.RESULT_COMPLETE表示操作成功，为SMSSDK.RESULT_ERROR表示操作失败。

* 当result=SMSSDK.RESULT_ERROR，则data的类型为Throwable；如果服务器有返回错误码，那么这个Throwable的message就存放着服务器返回的json数据，你可以从中读取相关信息。示例如下：

```java
//#if def{lang} == cn
// 根据服务器返回的网络错误，给toast提示
//#elif def{lang} == en
// show toast according to the error code
//#endif
try {
     Throwable throwable = (Throwable) data;
     throwable.printStackTrace();
     JSONObject object = new JSONObject(throwable.getMessage());
     String des = object.optString("detail");//错误描述
     int status = object.optInt("status");//错误代码
     if (status > 0 && !TextUtils.isEmpty(des)) {
	Toast.makeText(activity, des, Toast.LENGTH_SHORT).show();
	return;
     }
} catch (Exception e) {
     //do something							
}
```

* 当result=SMSSDK.RESULT_COMPLETE，则data的类型如下表所示。onUnregister在被反注册的时候被触发。

<table>
<tbody>
<tr>
<th>EVENT</th>
<th>DATA类型</th>
<th>说明</th>
</tr>
<tr>
<td>EVENT_GET_SUPPORTED_COUNTRIES</td>
<td>ArrayList&lt;HashMap&lt;String,Object&gt;&gt;</td>
<td>返回支持发送验证码的国家列表</td>
</tr>
<tr>
<td>EVENT_GET_VERIFICATION_CODE</td>
<td>Boolean</td>
<td>true为智能验证，false为普通下发短信</td>
</tr>
<tr>
<td>EVENT_SUBMIT_VERIFICATION_CODE</td>
<td>HashMap&lt;String,Object&gt;</td>
<td>校验验证码，返回校验的手机和国家代码</td>
</tr>
<tr>
<td>EVENT_GET_CONTACTS</td>
<td>ArrayList&lt;HashMap&lt;String,Object&gt;&gt;</td>
<td>获取手机内部的通信录列表</td>
</tr>
<tr>
<td>EVENT_SUBMIT_USER_INFO</td>
<td>null</td>
<td>提交应用内的用户资料</td>
</tr>
<tr>
<td>EVENT_GET_FRIENDS_IN_APP</td>
<td>ArrayList&lt;HashMap&lt;String,Object&gt;&gt;</td>
<td>获取手机通信录在当前应用内的用户列表</td>
</tr>
<tr>
<td>EVENT_GET_VOICE_VERIFICATION_CODE</td>
<td>null</td>
<td>请求发送语音验证码，无返回</td>
</tr>
</tbody>
</table>

*短信SDK采用“广播”的方式发送操作结果*。这就是说，每次调用registerEventHandler都会设置一个新的EventHandler到SDK内部，当事件发生时，这些注册进来的EventHandler都能收到信息而不会发生“后者替换前者”的问题。**为了避免EventHandler注册后不再使用而造成内存泄漏，请务必在确定不使用某个EventHandler时，调用反注册代码将其注销**，反注册的方法为：

```java
SMSSDK.unregisterEventHandler(EventHandler);
```

  在EventHandler的4个回调方法都可能不在UI线程下，因此如果要在其中执行UI操作，请**务必使用Handler发送一个消息给UI线程处理**。
