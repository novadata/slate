#返回码
当请求发生执行完毕之后，服务器会返回代码 ( code ) 和信息 ( msg ) 以及详细信息( detail )，完整的返回码如下表。

错误分为 客户端错误 和 服务器端错误 两种，如果是客户端错误，说明该错误是由用户提交的API引起的；如果是服务器端错误，说明该错误是由服务器端引起的。

##1.普通情况

代码 | 描述 | 提示
---- |-----| -----
0 | 成功执行 | 请求成功执行
-1 | 未知原因 | 请求出现未知错误


##2.客户端错误

<table>
<colgroup>
<col width="20%">
<col width="30%">
<col width="50%">
</colgroup>
<thead valign="bottom">
<tr class="row-odd"><th class="head">代码</th>
<th class="head">错误类型</th>
<th class="head">提示</th>
</tr>
</thead>
<tbody valign="top">
<tr class="row-even"><td>1100</td>
<td>消息格式错误</td>
<td>当缺少必要参数，或者参数值格式不正确时，会返回该错误。此时可以查看相关文档确认每个参数的格式是否正确。</td>
</tr>
<tr class="row-odd"><td>1200</td>
<td>身份验证失败</td>
<td><div class="first last line-block">
<div class="line">当用户提供了不存在的access_key_id参数，或者API请求的签名不正确时，会返回该错误。</div>
<div class="line">此时可以检查access_key_id和secret_access_key是否配置正确，或者参考我们的签名文档 <a class="reference internal" href="#签名方法"><em>签名方法</em></a> 检查是否签名有误。</div>
</div>
</td>
</tr>
<tr class="row-even"><td>1300</td>
<td>消息已过期</td>
<td>当用户提交的 <tt class="docutils literal"><span class="pre">time_stamp</span></tt> 参数时间过期(超过60秒)时会返回该错误。关于
<tt class="docutils literal"><span class="pre">time_stamp</span></tt> ，可以参考 <a class="reference internal" href="#公共参数"><em>公共参数</em></a></td>
</tr>
<tr class="row-odd"><td>1400</td>
<td>访问被拒绝</td>
<td><div class="first last line-block">
<div class="line">当用户试图访问不属于自己的资源，或者对于提交的操作没有权限，或者试图更改的资源处于不可更改的状态时等，会返回该错误。</div>
<div class="line">我们会在返回的错误信息里头提及错误原因。</div>
</div>
</td>
</tr>
<tr class="row-even"><td>2100</td>
<td>找不到资源</td>
<td>当用户试图访问不存在的资源时，会返回该错误。</td>
</tr>
<tr class="row-odd"><td>2400</td>
<td>余额不足</td>
<td>当用户余额不足时，会返回该错误。</td>
</tr>
<tr class="row-even"><td>2500</td>
<td>超过配额</td>
<td><div class="first last line-block">
<div class="line">当用户试图创建的资源超过用户配额，或者短时间内的API访问请求超过配额限制，会返回该错误。</div>
<div class="line">您可以通过提交工单的方式来向我们申请提高配额，并说明你申请的理由。</div>
</div>
</td>
</tr>
</tbody>
</table>

##3.服务端错误

<table>
<colgroup>
<col width="20%">
<col width="30%">
<col width="50%">
</colgroup>
<thead valign="bottom">
<tr class="row-odd"><th class="head">代码</th>
<th class="head">错误类型</th>
<th class="head">提示</th>
</tr>
</thead>
<tbody valign="top">
<tr class="row-even"><td>5000</td>
<td>内部错误</td>
<td>当服务器执行请求过程中，遇到未知错误时，会返回该错误信息。遇到这种错误，请及时与我们联系。</td>
</tr>
<tr class="row-odd"><td>5100</td>
<td>服务器繁忙</td>
<td>当服务器执行超时，或者服务器负载过高无法完成请求时，会返回该错误信息。遇到这种错误，请稍后再尝试，或者及时与我们联系。</td>
</tr>
<tr class="row-even"><td>5200</td>
<td>资源不足</td>
<td>当我们后台计算资源不足以满足用户的创建需求时，会返回该错误信息。遇到这种错误，请及时与我们联系。</td>
</tr>
<tr class="row-odd"><td>5300</td>
<td>服务更新中</td>
<td>当我们的后台服务正在更新时，可能会返回该错误信息。遇到这种错误，请稍后再尝试，或者及时与我们联系。</td>
</tr>
</tbody>
</table>

##返回样例

> 正常情况

````json
{
  "detail":"success",
  "msg":"success",
  "code":0
}
````
> 错误情况

````json
{
  "detail":"request param error",
  "msg":"请求参数错误,[websiteId]为必须参数",
  "code":1100
}
````