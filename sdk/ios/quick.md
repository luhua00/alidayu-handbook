# iOS快速集成

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

## 5. 使用SDK提供的API
![此处输入图片的描述][5]
SMSSDK.h文件为SMSSDK的核心类。这个类的所有方法以类方法提供，根据需要直接使用相应的方法。
短信UI部分是以Demo形式开源的，具体参考Demo。以下是Demo中部分UI模块的说明:

- 弹出注册页面
```objc
RegViewController* reg = [[RegViewController alloc] init];
[self presentViewController:reg animated:YES completion:^{
 
}];
```
注册界面RegViewController，进入该界面后，可选择国家和地区和输入手机号码进行短信注册验证；

选择国家和地区的选项进入SectionsViewController，在该控制器会从服务端获取所支持的国家和地区；

用户在注册页面输入手机号码之后，点击下一步，界面跳转到短信验证界面同时服务端会发送短信验证码到手机；

短信验证界面对应的控制器为VerifyViewController 主要处理短信的验证功能。

- 弹出通讯录好友界面
```objc
[_testView setNumber:0];
 
    SectionsViewControllerFriends* friends = [[SectionsViewControllerFriends alloc] init];
 
    _friendsController = friends;
 
    [_friendsController setMyBlock:_friendsBlock];
 
    [SMS_MBProgressHUD showMessag:NSLocalizedString(@"loading", nil) toView:self.view];
 
    [SMSSDK getAllContactFriends:1 result:^(NSError *error, NSArray *friendsArray) {
 
        if (!error) {
 
            [_friendsController setMyData:[NSMutableArray arrayWithArray:friendsArray]];
 
            [self presentViewController:_friendsController animated:YES completion:^{
                ;
            }];
        }
 
    }];
 
    //判断用户通讯录是否授权
    if (_alert1)
    {
        [_alert1 show];
    }
 
    if(ABAddressBookGetAuthorizationStatus() != kABAuthorizationStatusAuthorized && _alert1 == nil)
    {
        NSString* str = [NSString stringWithFormat:NSLocalizedString(@"authorizedcontact", nil)];
        UIAlertView* alert = [[UIAlertView alloc] initWithTitle:NSLocalizedString(@"notice", nil)
                                                      message:str
                                                     delegate:self
                                            cancelButtonTitle:NSLocalizedString(@"sure", nil)
                                            otherButtonTitles:nil, nil];
        _alert1 = alert;
        [alert show];
    }
```
通讯录好友界面SectionsViewControllerFriends，在事件响应中弹出该好友控制器即可；进入该界面后 会把通讯录的好友分为两类：

  - 已加入的用户：可以点击添加按钮，直接添加已加入该app的通讯录好友。
  - 待邀请的用户：可以点击邀请按钮发送短信邀请好友加入该app。


  [1]: http://wiki.mob.com/wp-content/uploads/2014/09/SMSSDK.jpg
  [2]: http://wiki.mob.com/wp-content/uploads/2014/09/SMS_SKD-drag.png
  [3]: http://wiki.mob.com/wp-content/uploads/2014/09/SMSSDKAddFramework.png
  [4]: http://www.mob.com
  [5]: http://wiki.mob.com/wp-content/uploads/2014/09/%E7%9F%AD%E4%BF%A1%E4%BB%A3%E7%A0%81.jpg