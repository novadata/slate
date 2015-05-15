---
title: NovaData API Reference

language_tabs:
  - curl

toc_footers:
  - <a href='http://app.novadata.cn/tasks/list'>进入控制台</a>
  - <a href='http://github.com/tripit/slate'>Docs Powered by Slate</a>

includes:
  - errors

search: true
---

#  介绍

Novadata向用户开放所有数据服务相关的API，API通过 HTTPS GET和HTTP GET两种 方式来进行调用的。在调用API之前，您需要先在控制台申请 API密钥 ， 包括 API密钥ID ( access_key_id ) 和 API密钥的私钥 ( secret_access_key )。 API密钥ID 将作为参数包含在每一个请求中发送；而 API密钥的私钥 负责生成请求串的签名，API密钥的私钥 需要被妥善保管，请勿外传。

##API 请求结构

Name|	Description	|Notes
----|----|-----|
API入口|	API 调用的 入口|	如：https://api.novadata.com/data/。 完整的列表请参见 [API列表](API列表)。
公共参数	|每个 API 调用都需要包含公共参数|	详情可见 [公共参数](公共参数) 
特定参数	|每个API特定的参数| 请参见[API列表](API列表)中的相关文档。

##API 请求样例

一个典型的API请求如下所示，这是一个 分页获取数据的API请求:(为了方便查看，我们将文档中的请求都进行换行来显示:)

````
https://api.novadata.cn/v1/data/query?access_key_id=NOVADATAACCESSKEYIDEXAMPLE
&limit=200
&signature_version=1
&signature=ihPnXFgsg5yyqhDN2IejJ2%2Bbo89ABQ1UqFkyOdzRITY%3D
````
## API 返回结构
返回结果为 JSON 格式，总体格式如下：

Name|	Description	|Notes
----|----|-----|
code|int|	返回码,用来表示API请求的返回值，参见[返回码](返回码)
msg|	string	|对返回码的详情说明，参见[返回码](返回码)
results	|json	|返回结果集,请参见每个[API列表](API列表)的相关文档
total_count |long	| 根据过滤条件得到的数据总数,请参见每个[API列表](API列表)的相关文档

## API 公共参数
公共参数是所有 API 请求都必需的参数。

<aside class="notice">
注解 为减少内容重复，公共参数就不在各API中列出了。
</aside>


公共参数:

Name	|Description	|Notes|
-------|----------|---------|
access_key|	access key|例如”NovaACCESSKEYIDEXAMPLE”。	 	 
signature_version |签名函数的版本号|目前只支持版本号为 1,签名所用哈希算法为HmacSHA256。	 
signature	|请求消息的签名|请参见 [签名方法](签名方法)

#签名方法

这里介绍API请求中签名 ( signature ) 的生成方法。签名需要你先在控制台创建 API密钥 ，获得 accesss_key_id 和 secret_access_key.
> 这里我们假设

````shell
access_key_id = 'NOVADATAACCESSKEYIDEXAMPLE'
secret_access_key = 'SECRETACCESSKEY'
````
> 例如我们的请求参数如下:

````json
{
  "limit":2,
  "offset":10,
  "fields":"data.*",
  "sort":"price:desc",
  "signature_version":1,
  "access_key_id":"NOVADATAACCESSKEYIDEXAMPLE",
}
````

> 注解 你可以使用上述的 AccessKey 和 Request 调试你的代码， 当得到跟后面一致的签名结果后(即表示你的代码是正确的)， 可再换为你自己的 AccessKey 和其他 API 请求。




## 1.按参数名进行升序排列
> 排序后的参数为:

````json
{
  "access_key_id":"NOVADATAACCESSKEYIDEXAMPLE",
  "fields":"data.*",
  "limit":2,
  "offset":10,
  "signature_version":1,
  "sort":"price:desc"
}
````


## 2.对参数名称和参数值进行URL编码
> 编码后的请求串为:

````json
{
  "access_key_id":"NOVADATAACCESSKEYIDEXAMPLE",
  "fields":"data.*",
  "limit":2,
  "offset":10,
  "signature_version":1,
  "sort":"price%3Adesc",
}
````

## 3.构造URL请求

参数名和参数值之间用”=”号连接，参数和参数之间用”＆”号连接，构造后的URL请求为

````
access_key_id=NOVADATAACCESSKEYIDEXAMPLE&fields=data.*&limit=2&offset=10&signature_version=1&sort=price%3Adesc
````

## 4.构造被签名串

被签名串的构造规则为: 被签名串 = HTTP请求方式 + "\n" + uri + "\n" + url 请求串


> 注解 "\n" 是换行符，不要将 "\" 转义。也就是说，不要用 "\\n" ，有些语言，比如php和ruby，请用 "\n" , 而不是 '\n'


> 假设 HTTP 请求方法为 GET, 请求的uri路径为 "/v1/data/websites/1", 则被签名串为

````
GET\n/v1/data/websites/1\naccess_key_id=NOVADATAACCESSKEYIDEXAMPLE&fields=data.*&limit=2&offset=10&signature_version=1&sort=price%3Adesc
````


## 5.计算签名

> 以 Python 代码为例 (其他语言类似，需要使用 sha256 + base64 编码，最后需要再进行 URL 编码，URL 编码时需要将原有的空格 ” ” 转为 “+”)

````python
import base64
import hmac
import urllib
from hashlib import sha256

h = hmac.new(secret_access_key, digestmod=sha256)
h.update("string_to_sign") # 前面生成的被签名串
sign = base64.b64encode(h.digest()).strip()
signature = urllib.quote_plus(sign)
````

> Java计算SHA256哈希。Base64编码可以使用apache commons codec,[完整示例代码](https://gist.github.com/fivesmallq/9482ca783578f1356243)

````java
    private static String calculateSignature(String key, String stringToSign) {
        final String ENCODING = "UTF-8";
        final String ALGORITHM = "HmacSHA256";
        byte[] signData = null;
        try {
            Mac mac = Mac.getInstance(ALGORITHM);
            mac.init(new SecretKeySpec(key.getBytes(ENCODING), ALGORITHM));
            signData = mac.doFinal(stringToSign.getBytes(ENCODING));
        } catch (InvalidKeyException e) {
        } catch (NoSuchAlgorithmException e) {
        } catch (UnsupportedEncodingException e) {
        } catch (IllegalStateException e) {
        }
        return new String(Base64.encodeBase64(signData));
    }
````



* 将API密钥的私钥 ( secret_access_key ) 作为key，生成被签名串的 HMAC-SHA256 签名，更多信息可参见 [RFC2104](http://www.ietf.org/rfc/rfc2104.txt)
* 将签名进行 Base64 编码
* 将 Base64 编码后的结果进行 URL 编码 （注意，当 Base64 编码后存在空格时，不要对空格进行 URL 编码，而要直接将空格转为”+”）
* 将签名用于参数”signature”的值。

## 6.添加签名
将签名参数附在原有请求串的最后面。最终的HTTP请求串为(为了查看方便，我们人为地将参数之间用回车分隔开)

````
access_key_id=NOVADATAACCESSKEYIDEXAMPLE
&fields=data.*
&limit=2
&offset=10
&signature_version=1
&sort=price%3Adesc
&signature=B9willCeoxK2KJLoZNn%2BOXl%2FiXE3Mu815P6y3KLn3CE%3D
````
> 完整的请求URL为(为了查看方便，我们人为地将参数之间用回车分隔开)

````
https://api.novadata.cn/v1/data/website/1?access_key_id=NOVADATAACCESSKEYIDEXAMPLE
&fields=data.*
&limit=2
&offset=10
&signature_version=1
&sort=price%3Adesc
&signature=B9willCeoxK2KJLoZNn%2BOXl%2FiXE3Mu815P6y3KLn3CE%3D
````
> 实际URL为

````
https://api.novadata.cn/v1/data/website/1?access_key_id=NOVADATAACCESSKEYIDEXAMPLE&fields=data.*&limit=2&offset=10&signature_version=1&sort=price%3Adesc&signature=B9willCeoxK2KJLoZNn%2BOXl%2FiXE3Mu815P6y3KLn3CE%3D
````

# Data API

## Get Data

获取单条数据

```shell
curl "http://api.novadata.cn/v1/data/1"
```

> The above command returns JSON structured like this:

```json
{
	"code":0,
	"results":{
		"current":true,
		"data":{
			"商品价格":"9.90",
			"商品全名":"黑胡椒牛排150g",
			"总体满意度":"4.9分",
			"评论数":"188",
			"顶级类别":"蛋肉家禽"
		},
		"id":"550be474cf147f2588d3f5cc",
		"navigateUrl":"http://www.yummy77.com/category/{page}-98-9804-0-5-p-.html",
		"timestamp":1426842740377,
		"url":"http://www.yummy77.com/product/211013013.html"
	},
	"total_count":1
}
```
### HTTP Request

`GET http://api.novadata.cn/v1/data/<ID>`


### URL Parameters

Parameter | Description
--------- | -----------
ID | 要获取数据的 id

##  Delete Data	

删除单条数据


```shell
curl "http://api.novadata.cn/v1/data/1"
```

> The above command returns JSON structured like this:

```json
{
	"code":2100,
	"msg":"找不到资源"
}
```

### HTTP Request

`DELETE http://api.novadata.cn/v1/data/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | 要删除数据的 id


##  Query Website Data	


```shell
curl "http://api.novadata.cn/v2/data/websites/551e39610fb408c079b91a82?q=&sort=&offset=2&limit=2&access_key_id=NOVADATAACCESSKEYIDEXAMPLE&signature_version=1&signature=ihPnXFgsg5yyqhDN2IejJ2%2Bbo89ABQ1UqFkyOdzRITY%3D&version=1"
```

> The above command returns JSON structured like this:

```json
{
	"code":0,
	"results":[
		{
			"current":true,
			"data":{
				"产品":"常年供应山鸡 量大优惠",
				"产地":"产地：山东省 菏泽市",
				"价格":"50.00",
				"供应商":"嘉祥县阳光食品销售部",
				"起订量":"1 - 5000"
			},
			"id":"551e42fb5fb4efc40c7de8b4",
			"navigateUrl":"http://search.cnhnb.com/product/list?page.orderAsc=false&page.pageNumber=1",
			"timestamp":1428046585666,
			"url":"http://www.cnhnb.com/product/137344"
		},
		{
			"current":true,
			"data":{
				"产品":"百恋鲩鱼（草鱼）",
				"产地":"产地：湖南省 长沙市",
				"价格":"398.00",
				"供应商":"湖南百恋渔业科技有限公司",
				"起订量":"1 - 10000"
			},
			"id":"551e43265fb4bb6099a375f9",
			"navigateUrl":"http://search.cnhnb.com/product/list?page.orderAsc=false&page.pageNumber=1",
			"timestamp":1428046629416,
			"url":"http://www.cnhnb.com/product/327643"
		}
	],
	"total_count":23295
}

```

根据查询条件q,分页查询指定website，从起始位置offset开始，其后的limit条数据.

<aside class="note">http://api.novadata.cn/v2/data/websites/552f2304a21471c6dc48f7f9?limit=100&q=data.商品名称:* 可以查询商品名称不为空的数据，q=data.商品名称:(* AND "水果") 可以查询商品名称不为空并且包含水果的数据。</aside>

### HTTP Request

`GET http://api.novadata.cn/v2/data/websites/<WebsiteId>`

### URL Parameters

Parameter | Description
--------- | -----------
WebsiteId | 要查询数据的 website id

### Query Parameters

	Parameter|	Type	|Required	|Description
    ----|----|----|----
	q| string | No| condition expression, see[Query String](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/query-dsl-query-string-query.html)
	sort| string|No|排序json表达式,默认按数据id升序。例如`post_date:asc,user_name:desc`多个条件以逗号分隔	
	offset| int | No | 起始数据所处偏移,first record  is 0,default value:0
 	limit|	int|	No	|数据集大小,Default value:100,max value: 10000
 	fields|	string|	No	|要返回的字段,默认为空,可以使用``data.title``来只返回 data 的 title 字段，多个字段用逗号隔开``data.title,data.body``,也可以使用``data.*``只返回 data 的全部数据
 	 start|	long|	No	| 查询数据的起始时间戳
 	 end|	long|	No	| 查询数据的结束时间戳
 	 debug|	boolean|	No	|  是否开启 debug，默认为 false，开启debug 模式会返回每条数据对应的 source 数据


##  Archive Website Data


```shell
curl "http://api.novadata.cn/v2/data/websites/551e39610fb408c079b91a82?q=&sort=&offset=2&limit=2&access_key_id=NOVADATAACCESSKEYIDEXAMPLE&signature_version=1&signature=ihPnXFgsg5yyqhDN2IejJ2%2Bbo89ABQ1UqFkyOdzRITY%3D&version=1"
```

> The above command returns JSON structured like this:

```json
{
	"code":0,
	"results":{
		"download_url":"http://novadata.qiniudn.com/551e39610fb408c079b91a82-553623781bb427ffe372c72f.json?download",
		"download_count":2,
		"file_size":"862 bytes"
	},
	"total_count":23297
}

```

根据websiteId获取起始位置offset后的limit条数据，将这些数据打包archive上传到qiniu进行分发，将对应下载链接返回给用户

### HTTP Request

`GET http://api.novadata.cn/v2/data/websites/<WebsiteId>/archive`

### URL Parameters

Parameter | Description
--------- | -----------
WebsiteId | 要归档数据的 website id

### Query Parameters

	Parameter|	Type	|Required	|Description
	----|----|----|----
	q| string | No| condition expression, see[Query String](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/query-dsl-query-string-query.html)
	sort| string|No|排序json表达式,默认按数据id升序。例如`post_date:asc,user_name:desc`多个条件以逗号分隔	
	offset| int | No | 起始数据所处偏移,first record  is 0,default value:0
 	limit|	int|	No	|数据集大小,Default value:100,max value: 10000
 	fields|	string|	No	|要返回的字段,默认为空,可以使用``data.title``来只返回 data 的 title 字段，多个字段用逗号隔开``data.title,data.body``,也可以使用``data.*``只返回 data 的全部数据
 	 start|	long|	No	| 查询数据的起始时间戳
 	 end|	long|	No	| 查询数据的结束时间戳
