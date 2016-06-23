# Android快速集成

使用2.0.0版本请注意：

1. 2.0.0版本的appkey与旧版本**不共用**，**需重新申请**
2. 测试期间短信条数限制：20条/天，APP开发完成后务必提交到mob.com后台审核，开通完全免费短信
3. 2.0.0之前版本的appkey无需审核，也不受此影响。

**目前检查到如果你编译的targetSdkVersion等于23，MINU的Android6.0在拒绝授权会关闭APP，所以建议用户将targetSdkVersion设成22或一下**

***

## 1. 获取SDK

请到[我们官网](http://www.mob.com/#/downloadDetail/SMS/android)按IDE的不同下载最新版本的SDK。我们分别提供了Android Studio和Eclipse的集成包。
下载回来后解压，可以看到下面的文件结构：

![SDK目录结构](http://wiki.mob.com/wp-content/uploads/2016/06/SDK-FOLDER.png)

- ApiDoc 目录存放接口的JavaDoc文档
- SMSSDK 目录存放的是短信SDK的全部内容
- Demo\_on_Github 是demo的github链接
- Download\_SMSSDKSignCal_APK 是下载计算应用签名的链接
- SMSSDK\_User_Guide 是官方文档的链接地址
- Update Log.txt 是SDK更新日志

附：[视频教材](http://v.youku.com/v_show/id_XODQ3MzQwMzA4.html)

## 2. 导入SDK
### 2.1 Eclipse IDE
短信SDK使用项目依赖的方式完成集成。具体步骤如下：

1. 复制“SMSSDK”目录到您自己项目的同一目录下，并将其导入到Eclipse中（[查看视频教程](http://v.youku.com/v_show/id_XODQ3MzM5OTI4.html)）
![添加到Eclipse](http://demo.mob.com/wiki/wp-content/uploads/2014/09/smssmdk_qi_21.png)
2. 右键您的项目，选择“属性”在弹出的窗口中侧栏选择“Android”，并在引用项目中选中“SMSSDK”（[查看视频教程](http://v.youku.com/v_show/id_XODQ3MzQwNTA0.html)）
![添加依赖](http://demo.mob.com/wiki/wp-content/uploads/2014/09/smssmdk_qi_31.png)

### 2.2 Android Studio

1. 文件说明

|              文件名   |       说明     |   需求   |
| --------------------- | -------------- | -------- |
|MobCommons.jar         | Mob通用公共库  |   必须   |
|MobTools.jar           | Mob工具公共库  |   必须   |
|SMSSDK-<version>.aar   |SMSSDK核心      |   必须   |
|SMSSDKGUI-<version>.aar|SMSSDK GUI开源库|   可选   |

如果你需要用GUI库，则需要加入全部这四个文件，不需要可以去掉SMSSDKGUI。为了方便和ShareSDK一起集成，公共库依然打包成jar。
如果你同时使用ShareSDK,保留一份公共库就行（公共库版本一致或兼容）。

2. 如何集成

Studio使用aar文件(放在SMSSDK目录下)集成，更方便快捷。步骤如下：

1. 将以上文件按需放入Android Studio项目所要使用SMSSDK的Module所在的Libs里面，如图
![studio目录](http://wiki.mob.com/wp-content/uploads/2014/09/AARTest.png)

2. 再在Module的build.gradle里面将libs加入仓库（repositories），然后添加依赖（dependencies ）示例如下：
```
repositories{
    flatDir{
        dirs 'libs' //就是你放aar的目录地址
    }
}
dependencies {
    ....//你的其他依赖
    compile name:'SMSSDK-<version>',ext:'aar'
    compile name:'SMSSDKGUI-<version>',ext:'aar'
}
```

![添加dependencies](http://wiki.mob.com/wp-content/uploads/2014/09/buildgradle.png)

## 3. 配置AndroidManifest.xml

- 打开您项目的“AndroidManifest.xml”，在其中添加如下的权限：
```xml
<uses-permission android:name="android.permission.READ_CONTACTS" />
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.RECEIVE_SMS" />
<uses-permission android:name="android.permission.GET_TASKS" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

- 然后在“application”下添加如下activity：
```xml
<activity
android:name="com.mob.tools.MobUIShell"
android:theme="@android:style/Theme.Translucent.NoTitleBar"
android:configChanges="keyboardHidden|orientation|screenSize"
android:windowSoftInputMode="stateHidden|adjustResize"/>
```

## 4. 添加代码

### 启动SDK

在您的项目启动时，调用下面的代码：

`SMSSDK.initSDK(this, "您的appkey", "您的appsecret");`

### 发送短信验证码
短信SDK内置了开源的GUI功能，您可以通过调用下面的代码打开短信验证页面：

```java
//打开注册页面
RegisterPage registerPage = new RegisterPage();
registerPage.setRegisterCallback(new EventHandler() {
public void afterEvent(int event, int result, Object data) {
// 解析注册结果
if (result == SMSSDK.RESULT_COMPLETE) {
@SuppressWarnings("unchecked")
HashMap<String,Object> phoneMap = (HashMap<String, Object>) data;
String country = (String) phoneMap.get("country");
String phone = (String) phoneMap.get("phone"); 
// 提交用户信息
registerUser(country, phone);
}
}
});
registerPage.show(context);
```

其中EventHandler是短信SDK的操作回调，具体说明文档请参阅：[短信SDK操作回调](callback.html)章节。

### 显示通信录好友列表
同时，短信SDK也内置了通过本设备通信录换取本应用内用户列表的功能，可以通过下面的代码打开“通信录好友”页面：

```java
//打开通信录好友列表页面
ContactsPage contactsPage = new ContactsPage();
contactsPage.show(context);
```

# 参阅

1. [无GUI接口调用](gui.html)
2. [短信SDK更新历史](log.html)
3. [appkey防盗用须知](appkey.html)
4. [Android 短信智能验证说明（1.3.1新增）](intelligent.html)
