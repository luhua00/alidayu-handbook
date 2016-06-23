# 短信SDK无UI集成

## 1. 获取短信SDK
- SDK下载之后解压，等到下面文件

![SDK][1]
- 1、SMSSDK：短信验证码SDK，包括静态库和本地化文件。使用时直接将这个文件夹拖入工程。
- 2、SMSSDKDemo：示例Demo 。
- 3、如果想要集成SMSSDK 提供的UI，直接把SMSSDKUI.xcodeproj拖到程序。

## 2. 导入SDK
- 将SMSSDK这个文件夹拖入工程。步骤如下：

![此处输入图片的描述][2]

## 3. 添加依赖库文件
- 必要：

```objc
libz.dylib
libicucore.dylib
MessageUI.framework
JavaScriptCore.framework
libstdc++.dylib
```

- 可选

```objc
AddressBook.framework （通讯录功能需要）
AddressBookUI.framework（通讯录功能需要）
```
![此处输入图片的描述][3]
## 4. 添加初始化代码
- 1、在appDelegate 添加

```objc
#import <SMS_SDK/SMSSDK.h>
```
- 在

```objc
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
```
- 添加

```objc
//初始化应用，appKey和appSecret从后台申请得
   [SMSSDK registerApp:appKey
    withSecret:appSecret];
```
- appKey 和 appSecret的获取：
 - 到[Mob官网][4]注册成为Mob开发者；
 - 到应用管理后台创建应用；

## 5. 创建获取验证码的按钮，或者是在自己已有的界面的按钮事件里调用获取验证码的方法

```objc
 /**
 *  @from                    v1.1.1
 *  @brief                   获取验证码(Get verification code)
 *
 *  @param method            获取验证码的方法(The method of getting verificationCode)
 *  @param phoneNumber       电话号码(The phone number)
 *  @param zone              区域号，不要加"+"号(Area code)
 *  @param customIdentifier  自定义短信模板标识 该标识需从官网http://www.mob.com上申请，审核通过后获得。(Custom model of SMS.  The identifier can get it  from http://www.mob.com  when the application had approved)
 *  @param result            请求结果回调(Results of the request)
 */

[SMSSDK getVerificationCodeByMethod:SMSGetCodeMethodSMS phoneNumber:@"159****1689"
                                                               zone:@"86"
                                                   customIdentifier:nil
                                                             result:^(NSError *error){
       if (!error) {
            NSLog(@"获取验证码成功");
        } else {
            NSLog(@"错误信息：%@",error);
        }];
```

## 6. 创建提交验证码的按钮，或者是在自己已有的界面的按钮事件里调用提交验证码的方法
```objc
[SMSSDK commitVerificationCode:self.verifyCodeField.text phoneNumber:_phone zone:_areaCode result:^(NSError *error) {

            if (!error) {
                NSLog(@"验证成功");
            }
            else
            {
                NSLog(@"错误信息:%@",error);
            }
        }];
```

- 基本只要调以上3个方法就OK了，基本就完成了我们的短信sdk的集成了，如果要做其他的功能，具体可以看sdk里的方法哦！
