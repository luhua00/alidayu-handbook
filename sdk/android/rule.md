# SDK的默认规则

- 开发者新申请的AppKey默认开启发送**中国大陆**的短信权限，每天最多发送**20**条验证码短信，包括：**文本验证码短信**和**语音验证码短信**。


- 开发者如果需要发送港澳台，国外短信需要到开发者后台打开对应地区的发送开关。


- 开发者如果需要校验验证码由自己来做，请到开发者后台打开服务端验证开关。


- iOS同时集成SMSSDK和ShareSDK时，请注意替换最新的**公共库**。


- Android集成SMSSDK时，请注意Android版本的SDK拥有智能验证的功能，默认：**开启**。

> 所谓智能验证，是指同一个手机号在同一个应用中同一台Android手机上校验成
> 功后，再次请求发送验证码时，不会真实发送验证码短信，我们认为本次请求合
> 法且真实，直接返回成功。
  
  
- 默认同一手机号在同一个应用下每分钟最多可发送2条验证码短信，包括：客户端发送的文本验证码短信，客户端发送语音验证码短信，Web-api发送的文本验证码短信。


- 同一个AppKey每分钟最多发送100条短信，包括：客户端发送的文本和语音验证码短信，Web-api发送的文本验证码短信，自定义短信。如果AppKey中发送短信的数量超过100的次数超过5次，则当前AppKey会被mob平台加入黑名单，当前AppKey不再拥有发送短信的权限，当AppKey被加入到黑名单中时，mob会向您发送提醒邮件和短信。此规则每天凌晨归零。


- 如果AppKey没有提交应用安装包给mob审核，则每个AppKey每天只能发送20条短信，包括：客户端发送的文本验证码短信，客户端发送语音验证码短信。


- 在mob平台每个手机号码12小时内可发送文本（客户端+webApi），语音，自定义短信各5条。开发者可申请提升发送上限到24小时内发送10条，此限制每天凌晨归零。


- 使用mob平台的web-api和自定短信接口需要充值后使用，即通过这两个接口发送的短信是收费的，而通过SMSSDk发送的短信全球免费。


- 使用Web-api和自定义短信接口的开发者请注意，当您的账户余额不足10元时，Mob会向您发送提醒邮件和短信。


- 如果您的账户欠费，并立刻影响您的接口使用，我们会根据您的历史充值额度的总和给您设定一个信誉额度。如，您的历史充值记录为：50元，100元，那么您的信誉额度是：150元，此时您的账户余额已经小于0元，您所使用的接口还能继续发送短信，直到您的账户欠费余额大于等于150时，我们将停止对您提供的服务，停止时会发送提醒邮件和短信。


- 请使用Web-api和自定义短信接口的开发者注意，请在您的发送入口处做防刷机制（加入图片验证码等方式），mob提供快速高效的发送通道，因此不能根据各个开发者设定详细的防刷规则。
