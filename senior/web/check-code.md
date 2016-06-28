---
date: 2016-06-28 17:27
status: public
title: Web-Api验证文本验证码
---

# Web-Api验证文本验证码接口

## 前言

Web-Api验证接口主要为了Web-Api发送验证码短信接口提供验证功能。
>**注意：
使用Web-Api验证接口是只针对[Web-Api发送接口](senior/web/send-text.md)使用，如果是客户端发送服务端校验，请使用[服务端校验接口](senior/server/server-verify.md)**

## 摘要
> **Web-Api**:发短信请求来自开发者自己的服务器，验证码的验证者是开发者自己的服务器。


## 接口使用
> **前提: 已成功接入Web-Api发送文本短信接口**

- 请求地址为：https://webapi.sms.mob.com/sms/checkcode
- 请求方式: POST
- 请求参数

|参数名 &nbsp;|参数类型 &nbsp;|是否是必要参数 &nbsp;|描述 &nbsp;|
|----------------|:-----------------|:-------------------------|:------------:|
|appkey|	string|	必填|应用appkey|
|zone|string|必填区号（国家代码）|区号（国家代码） &nbsp;|
|phone|	string|必填(去除区号的手机号 13121222212) &nbsp;|手机号码|
|code|	string|	必填|	验证码|
- 返回结果 [错误码](#error_code)

```json
{"status":200}
```

- 测试脚本

```sh
curl -d 'appkey=xxxx&amp;phone=xxxx&amp;zone=86&amp;code=xxxx'  'https://webapi.sms.mob.com/sms/checkcode'
```

## 样例代码

* PHP

```php
// 配置项
$api = '接口地址（例：https://webapi.sms.mob.com）';
$appkey = '您的appkey';
 
// 发送验证码
$response = postRequest( $api . 'sms/checkcode', array(
    'appkey' =&gt; $appkey,
    'phone' =&gt; '152xxxx4345',
    'zone' =&gt; '86',
    'code' =&gt; '1234',
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
            WebRequest request = WebRequest.Create("https://webapi.sms.mob.com/sms/checkcode");
            request.Proxy = null;
            request.Credentials = CredentialCache.DefaultCredentials;

            //allows for validation of SSL certificates 

            ServicePointManager.ServerCertificateValidationCallback += new System.Net.Security.RemoteCertificateValidationCallback(ValidateServerCertificate);
            byte[] bs = Encoding.UTF8.GetBytes("appkey=xxxxxxxx&amp;phone=xxxxxxxxxx&amp;zone=xx&amp;code=xxxx");
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

		String result = requestData("https://webapi.sms.mob.com/sms/checkcode",
 "appkey=xxxx&amp;phone=xxx&amp;zone=xx&amp;code=xxxx");
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
        self.verify_url = 'https://webapi.sms.mob.com/sms/checkcode'

    def verify_sms_code(self, zone, phone, code, debug=False):
        if debug:
            return 200

        data = {'appkey': self.appkey, 'phone': phone, 'zone': zone, 'code': code}
        req = requests.post(self.verify_url, data=data, verify=False)
        if req.status_code == 200:
            j = req.json()
            return j.get('status', 500)

        return 500

if __name__ == '__main__':
    mobsms = MobSMS('your_mob_sms_key_goes_here')
    print mobsms.verify_sms_code(86, 13900000000, '1234')
```

## 附录
**错误编码** <a id="error_code"></a> 

|返回值 &nbsp;|错误描述 &nbsp;|
|------|:--------:|
|200|	校验验证码成功|
|405|	请求参数中的appkey为空|
|406|	非法的appkey|
|456|	请求参数中的手机号码或者国家代码为空|
|457|	手机号码格式错误|
|467|	请求校验验证码频繁（5分钟校验超过3次）|
|468|	用户提交校验的验证码错误|
|469|	没有打开发送Http-api的开关|
|471|	请求ip和绑定ip不符|