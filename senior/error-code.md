#错误码

## 短信服务器错误码

| 序号     | 错误码 |  错误描述  			|说明|
| -------- | -----  | ----  	 			|----  |
| 1        | 400    | 无效请求 				|客户端请求不能被识别。|
| 2        | 401    | token为空 			|请求的token为空。|
| 3        | 402    | token不存在 			|请求的token不存在。  |
| 4        | 403  	| CRC32为空 			|请求Header中的hash为空。|
| 5        | 404    | CRC32非法 			|请求Header中的hash非法。  |
| 6        | 405    | AppKey为空 			|请求的AppKey为空。  |
| 7        | 406   	| AppKey错误 			|请求的AppKey不存在。  |
| 8        | 407    | 缺少数据 				|请求提交的数据缺少必要的数据。  |
| 9        | 408  	| 无效的参数 			|无效的请求参数。  |
| 10       | 418	| 内部接口调用失败 		|内部接口调用失败。 |
| 11       | 419	| duid对应的token不存在 |appKey_token对应的duid不存在  |
| 12       | 420	| duid不存在 			|duid不存在  |
