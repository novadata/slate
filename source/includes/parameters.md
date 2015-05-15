
# 公共参数
公共参数是所有 API 请求都必需的参数。

<aside class="notice">
注解 为减少内容重复，公共参数就不在各API中列出了。
</aside>


公共参数:

Name	|Description	|Notes|
-------|----------|---------|
access_key|	access key|例如”NovaACCESSKEYIDEXAMPLE”。	 	 
signature_version |签名函数的版本号|目前只支持版本号为 1,签名所用哈希算法为HmacSHA256。	 
signature	|请求消息的签名|请参见 [签名方法](#签名方法)