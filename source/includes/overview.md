#  介绍

Novadata向用户开放所有数据服务相关的API，API通过 HTTPS GET和HTTP GET两种 方式来进行调用的。在调用API之前，您需要先在控制台申请 API密钥 ， 包括 API密钥ID ( access_key_id ) 和 API密钥的私钥 ( secret_access_key )。 API密钥ID 将作为参数包含在每一个请求中发送；而 API密钥的私钥 负责生成请求串的签名，API密钥的私钥 需要被妥善保管，请勿外传。

##API 请求结构

Name|	Description	|Notes
----|----|-----|
API入口|	API 调用的 入口|	如：https://api.novadata.com/data/。 完整的列表请参见 [API列表](#api列表)。
公共参数	|每个 API 调用都需要包含公共参数|	详情可见 [公共参数](#公共参数) 
特定参数	|每个API特定的参数| 请参见[API列表](#api列表)中的相关文档。

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
code|int|	返回码,用来表示API请求的返回值，参见[返回码](#返回码)
msg|	string	|对返回码的详情说明，参见[返回码](#返回码)
results	|json	|返回结果集,请参见每个[API列表](#api列表)的相关文档
total_count |long	| 根据过滤条件得到的数据总数,请参见每个[API列表](#api列表)的相关文档
