# CocoaPods 集成SMSSDK
## 1. 获取AppKey
- 如何在我们的官网注册应用得到appkey，请点击链接看里面的操作步骤。
## 2. 通过CocoaPods安装
- 通过 CocoaPods进行安装，只需在 Podfile文件中添加，添加之后执行 pod install 命令
```
   // Mob产品公共库
   pod 'MOBFoundation_IDFA'
   // SMSSDK必须
   pod 'SMSSDK'
```

## 3. 快速集成
- 获取验证码的方法
```objc
/**
 *  @from                    v1.1.1
 *  @brief                   获取验证码(Get verification code)
 *
 *  @param method            获取验证码的方法(The method of getting verificationCode)
 *  @param phoneNumber       电话号码(The phone number)
 *  @param zone              区域号，不要加"+"号(Area code)
 *  @param customIdentifier  自定义短信模板标识 该标识需从官网http://www.mob.com上申请，审核通过后获得。(Custom model of SMS.  The identifier can get it  from http://www.mob.com  when the application had approved)
 *  @param result            请求结果回调(Results of the request)
 */

[SMSSDK getVerificationCodeByMethod:SMSGetCodeMethodSMS phoneNumber:@"159****1689"
                                                               zone:@"86"
                                                   customIdentifier:nil
                                                             result:^(NSError *error)
       if (!error) {
            NSLog(@"获取验证码成功");
        } else {
            NSLog(@"错误信息：%@",error);
        }];
```
- 提交验证码的方法
```objc
/**
 * @from               v1.1.1
 * @brief              提交验证码(Commit the verification code)
 *
 * @param code         验证码(Verification code)
 * @param phoneNumber  电话号码(The phone number)
 * @param zone         区域号，不要加"+"号(Area code)
 * @param result       请求结果回调(Results of the request)
 */
   [SMSSDK commitVerificationCode:self.verifyCodeField.text phoneNumber:_phone zone:_areaCode result:^(NSError *error) {
 
            if (!error) {
 
                NSLog(@"验证成功");
 
            }
            else
            {
                NSLog(@"错误信息：%@",error);
 
            }
        }];
```

