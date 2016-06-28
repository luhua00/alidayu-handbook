---
date: 2016-06-28 16:11
status: public
title: Web-Api发送文本验证码短信
---

# 发送文本验证码短信接口

## 前言

Web-Api接口主要目的是为了解决非移动端用户，他们需要一个不依赖于移动设备的短信服务。短信的发起端，验证端都在服务器这样的一种需求。
>**注意：
使用Web-Api发送短信的开发者请做好防刷机制，由于Mob提供公共服务无法提供精确的防刷机制。**

## 摘要
很多开发者都会有一个疑问,WEB-API跟服务器验证的区别。
>**服务器验证**: 发起短信请求的是移动端(手机),验证码的验证者是开发者自己的服务器
> **WEB-API**:发送短信的请求是开发者自己的服务器，验证码的验证者也是开发者自己的服务器。

此接口发送的验证码有效时常是5分钟，超过5分钟验证将返回468验证码失效。

本文主要是描述WEB-API接口的功能以及调用。

## 接口使用
> **前提:开通WEB-API开关**
进入[**开发者后台**](http://dashboard.mob.com/#/sms/config/setting)设置
 应用管理→短信设置 
 >> 开启WEB-API接口开关
 入访问接口的服务器公网ip地址，多个地址用,隔开。
 如：127.0.0.1,192.168.10.1
![](http://wiki.mob.com/wp-content/uploads/2015/12/web-api.png)

> **获取服务器公网IP工具：** **http://ip.mob.com/**

> **使用方法** ： 
>> **Linux:** curl http://ip.mob.com/
>> **windows:** 点击[http://ip.mob.com/](http://ip.mob.com/)

### 接口使用
- 请求地址为：https://webapi.sms.mob.com/sms/sendmsg
- 请求方式: POST
- 请求参数

|参数名 &nbsp;|参数类型 &nbsp;|是否是必要参数 &nbsp;|描述 &nbsp;|
|----------------|:-----------------|:-------------------------|:------------:|
|appkey|	string|	必填|应用appkey|
|zone|string|必填区号（国家代码）|区号（国家代码） &nbsp;|
|phone|	string|必填(去除区号的手机号 13121222212) &nbsp;|手机号码|
- 返回结果 [错误码](#error_code)

```json
{"status":200}
```

- 测试脚本

```sh
curl -d 'appkey=xxxx&amp;phone=xxxx&amp;zone=86'  'https://webapi.sms.mob.com/sms/sendmsg'
```

## 样例代码

* PHP

```php
// 配置项
$api = '接口地址（例：https://webapi.sms.mob.com）';
$appkey = '您的appkey';
 
// 发送验证码
$response = postRequest( $api . 'sms/sendmsg', array(
    'appkey' =&gt; $appkey,
    'phone' =&gt; '152xxxx4345',
    'zone' =&gt; '86'
) );
 
/**
 * 发起一个post请求到指定接口
 * 
 * @param string $api 请求的接口
 * @param array $params post参数
 * @param int $timeout 超时时间
 * @return string 请求结果
 */
function postRequest( $api, array $params = array(), $timeout = 30 ) {
    $ch = curl_init();
    curl_setopt( $ch, CURLOPT_URL, $api );
    // 以返回的形式接收信息
    curl_setopt( $ch, CURLOPT_RETURNTRANSFER, 1 );
    // 设置为POST方式
    curl_setopt( $ch, CURLOPT_POST, 1 );
    curl_setopt( $ch, CURLOPT_POSTFIELDS, http_build_query( $params ) );
    // 不验证https证书
    curl_setopt( $ch, CURLOPT_SSL_VERIFYPEER, 0 );
    curl_setopt( $ch, CURLOPT_SSL_VERIFYHOST, 0 );
    curl_setopt( $ch, CURLOPT_TIMEOUT, $timeout );
    curl_setopt( $ch, CURLOPT_HTTPHEADER, array(
        'Content-Type: application/x-www-form-urlencoded;charset=UTF-8',
        'Accept: application/json',
    ) ); 
    // 发送数据
    $response = curl_exec( $ch );
    // 不要忘记释放资源
    curl_close( $ch );
    return $response;
}
```

* C#

感谢@金雷 开发者提供

```c#
 	public static void ConnectSSL()
        {
            WebRequest request = WebRequest.Create("https://webapi.sms.mob.com/sms/sendmsg");
            request.Proxy = null;
            request.Credentials = CredentialCache.DefaultCredentials;

            //allows for validation of SSL certificates 

            ServicePointManager.ServerCertificateValidationCallback += new System.Net.Security.RemoteCertificateValidationCallback(ValidateServerCertificate);
            byte[] bs = Encoding.UTF8.GetBytes("appkey=xxxxxxxx&amp;phone=xxxxxxxxxx&amp;zone=xx");
            request.Method = "Post";
            using (Stream reqStream = request.GetRequestStream())
            {
                reqStream.Write(bs, 0, bs.Length);
            } 
            HttpWebResponse response = (HttpWebResponse)request.GetResponse();
            Stream dataStream = response.GetResponseStream();
            StreamReader reader = new StreamReader(dataStream);
            string responseFromServer = reader.ReadToEnd();

        }

        //for testing purpose only, accept any dodgy certificate... 
        public static bool ValidateServerCertificate(object sender, X509Certificate certificate, X509Chain chain, SslPolicyErrors sslPolicyErrors)
        {
            return true;
        }
```

* JAVA

样例程序: [https://github.com/tian-github/Mob_SmsSpi](https://github.com/tian-github/Mob_SmsSpi)

```java
    public static void main(String[] args) throws Exception {

		String result = requestData("https://webapi.sms.mob.com/sms/sendmsg",
 "appkey=xxxx&amp;phone=xxx&amp;zone=xx&amp;");
        System.out.println(result);
	}

	/**
	 * 发起https 请求
	 * @param address
	 * @param m
	 * @return
	 */
	public  static String requestData(String address ,String params){

			HttpURLConnection conn = null;
			try {
			// Create a trust manager that does not validate certificate chains
			TrustManager[] trustAllCerts = new TrustManager[]{new X509TrustManager(){
			    public X509Certificate[] getAcceptedIssuers(){return null;}
			    public void checkClientTrusted(X509Certificate[] certs, String authType){}
			    public void checkServerTrusted(X509Certificate[] certs, String authType){}
			}};

			// Install the all-trusting trust manager
		    SSLContext sc = SSLContext.getInstance("TLS");
		    sc.init(null, trustAllCerts, new SecureRandom());

		    //ip host verify
		    HostnameVerifier hv = new HostnameVerifier() {
		         public boolean verify(String urlHostName, SSLSession session) {
		         return urlHostName.equals(session.getPeerHost());
		         }
		    };

		    //set ip host verify
			HttpsURLConnection.setDefaultHostnameVerifier(hv);

			HttpsURLConnection.setDefaultSSLSocketFactory(sc.getSocketFactory());

			URL url = new URL(address);
			conn = (HttpURLConnection) url.openConnection();
			conn.setRequestMethod("POST");// POST
			conn.setConnectTimeout(3000);
			conn.setReadTimeout(3000);
			// set params ;post params 
			if (params!=null) {
				conn.setDoOutput(true);
				DataOutputStream out = new DataOutputStream(conn.getOutputStream());
				out.write(params.getBytes(Charset.forName("UTF-8")));
				out.flush();
				out.close();
			}
			conn.connect();
			//get result 
			if (conn.getResponseCode() == HttpURLConnection.HTTP_OK) {
				String result = parsRtn(conn.getInputStream());
				return result;
			} else {
				System.out.println(conn.getResponseCode() + " "+ conn.getResponseMessage());
			}
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			if (conn != null)
				conn.disconnect();
		}
		return null;
	}
```

* Python

感谢@jimidata 开发者提供，谢谢支持

样例程序: [https://github.com/freeznet/py_mob_sms](https://github.com/freeznet/py_mob_sms)

```python
#!/usr/bin/env python
# encoding: utf-8
import requests

__author__ = 'rui'

class MobSMS:
    def __init__(self, appkey):
        self.appkey = appkey
        self.verify_url = 'https://webapi.sms.mob.com/sms/sendmsg'

    def verify_sms_code(self, zone, phone,  debug=False):
        if debug:
            return 200

        data = {'appkey': self.appkey, 'phone': phone, 'zone': zone}
        req = requests.post(self.verify_url, data=data, verify=False)
        if req.status_code == 200:
            j = req.json()
            return j.get('status', 500)

        return 500

if __name__ == '__main__':
    mobsms = MobSMS('your_mob_sms_key_goes_here')
    print mobsms.verify_sms_code(86, 13900000000)
```

## 附录
**错误编码** <a id="error_code"></a> 

|返回值 &nbsp;|错误描述 &nbsp;|
|------|:--------:|
|200|	发送或校验验证码成功|
|405|	请求参数中的appkey为空|
|406|	非法的appkey|
|456|	请求参数中的手机号码或者国家代码为空|
|457|	手机号码格式错误|
|458|	手机号码在黑名单中|
|463|	手机号码超出当天发送短信的限额|
|469|	没有打开发送Http-api的开关|
|470|	账户短信余额不足|
|471|	请求ip和绑定ip不符|
|477|	当前手机号码在SMSSDK平台内每天最多可发送短信10条，包括客户端发送和WebApi发送|
|478|	当前手机号码在当前应用下12小时内最多可发送文本验证码5条.|