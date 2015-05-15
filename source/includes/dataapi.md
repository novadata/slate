

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
