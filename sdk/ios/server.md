# 短信服务端验证接口 

## 1. 前言

>有些时候开发者需要验证服务托管自己的服务器（开发者服务器拥有校验验证码的功能），然后可以更好处理自己的业务逻辑。因此，Mob平台提供了此接口实现上述需求，供开发者使用。


- 此接口支持


**SMSSDK2.0.0以上版本（包括2.0.0）**



## 2. 摘要


> 本接口属于SMSSDK短信平台的开放验证服务,不提供短信发送服务,主要是承担验证APP使用SMSSDK发送的短信验证码，使用该接口来验证验证码是否正常。



简单梳理了一下流程，在没有短信服务端验证接口的时候，我的服务器需要知道用户是否验证成功是这样走的
![验证流程][1]

- 引入了服务端验证接口我们可以这样走,本接口开放了第五步动作

![验证流程][2]

## 3. 接口使用

### 第一步：开通服务端验证开关
免费短信验证码SDK/应用管理/短信设置 , 把服务端验证接口开关开启
![服务端验证接口开关开启][3]

### 第二步：请求接口验证短信

>请求地址为：https://webapi.sms.mob.com/sms/verify
>请求方式: POST
>请求参数

<table>
<tbody>
<tr>
<td valign="top" width="75">参数名</td>
<td valign="top" width="64">参数类型</td>
<td valign="top" width="218">描述</td>
<td valign="top" width="218">是否必填</td>
</tr>
<tr>
<td valign="top" width="75">appkey</td>
<td valign="top" width="64">string</td>
<td valign="top" width="218">应用appkey</td>
<td valign="top" width="218">必填</td>
</tr>
<tr>
<td valign="top" width="75">phone</td>
<td valign="top" width="64">string</td>
<td valign="top" width="218">电话号码</td>
<td valign="top" width="218">必填(不带区号电话号码 eg.13121222212)</td>
</tr>
<tr>
<td valign="top" width="75">zone</td>
<td valign="top" width="64">string</td>
<td valign="top" width="218">区号</td>
<td valign="top" width="218">必填(纯数字区号 eg.86)</td>
</tr>
<tr>
<td valign="top" width="75">code</td>
<td valign="top" width="64">string</td>
<td valign="top" width="218">需要验证的验证码</td>
<td valign="top" width="218">必填</td>
</tr>
</tbody>
</table>

- 返回结果

```js
{status:200}
```
- 测试脚本

```sh
curl -d 'appkey=xxxx&amp;phone=132****8362&amp;zone=86&amp;code=xxxx'  'https://webapi.sms.mob.com/sms/verify'
```

样例代码

- php

```php
<?php

// 配置项
$api = '接口地址（例：https://webapi.sms.mob.com)';
$appkey = '您的appkey';

// 发送验证码
$response = postRequest( $api . '/sms/verify', array(
    'appkey' => $appkey,
    'phone' => '152xxxx4345',
    'zone' => '86',
    'code' => '1234',
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

- C#

```c
public static void ConnectSSL()
        {

            WebRequest request = WebRequest.Create("https://webapi.sms.mob.com/sms/verify");
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

- jave 

```java
public static void main(String[] args) throws Exception {

        String result = requestData("https://webapi.sms.mob.com/sms/verify",
 "appkey=xxxx&amp;phone=xxx&amp;zone=xx&amp;&amp;code=xx");
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

- python

```python
#!/usr/bin/env python
# encoding: utf-8
import requests

__author__ = 'rui'

class MobSMS:
    def __init__(self, appkey):
        self.appkey = appkey
        self.verify_url = 'https://webapi.sms.mob.com/sms/verify'

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


  [1]: http://wiki.mob.com/wp-content/uploads/2014/12/old.png
  [2]: http://wiki.mob.com/wp-content/uploads/2014/12/SMS_NEW.png
  [3]: http://wiki.mob.com/wp-content/uploads/2015/12/%E6%9C%8D%E5%8A%A1%E7%AB%AF%E9%AA%8C%E8%AF%81%E5%BC%80%E5%85%B3-1024x874.png