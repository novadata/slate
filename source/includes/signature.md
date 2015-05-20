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