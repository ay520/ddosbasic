# ModifyPersistenceTimeOut {#doc_api_949169 .reference}

调用ModifyPersistenceTimeOut接口配置会话保持时间。

## 调试 {#apiExplorer .section}

单击[这里](https://api.aliyun.com/#product=DDoSPro&api=ModifyPersistenceTimeOut)在OpenAPI Explorer中进行可视化调试，并生成SDK代码示例。

## 请求参数 {#parameters .section}

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|ConfigJson|String|是|\{"persistence\_timeout":400\}|会话保持时间配置内容（JSON字符串格式），具体结构如下：

 -   **persistence\_timeout**，Integer类型，必选，设置会话保持时间，单位为秒，取值范围：30-3,600。

 **说明：** 此处的参数名以小写开头。

 |
|FrontPort|Integer|是|255|转发端口。

 |
|Ip|String|是|1.1.1.1|要操作的高防实例IP。

 |
|LbId|String|否|xxxxxxx-xxxx-xxxx-xxxxxxxx|高防IP策略修改标识。

 |

## 返回参数 {#resultMapping .section}

|名称|类型|示例值|描述|
|--|--|---|--|
|RequestId|String|C33EB3D5-AF96-43CA-9C7E-37A81BC06A1E|本次请求的ID。

 |

## 示例 {#demo .section}

请求示例

``` {#request_demo}

https://ddospro.cn-hangzhou.aliyuncs.com/?Action=ModifyPersistenceTimeOut
&Ip=1.1.1.1
&FrontPort=255
&ConfigJson={"persistence_timeout":400}
&LbId=xxxxxxx-xxxx-xxxx-xxxxxxxx
&公共请求参数

```

正常返回示例

`XML` 格式

``` {#xml_return_success_demo}
<root>
  <RequestId>C33EB3D5-AF96-43CA-9C7E-37A81BC06A1E</RequestId>
</root>

```

`JSON` 格式

``` {#json_return_success_demo}
{
	"RequestId":"C33EB3D5-AF96-43CA-9C7E-37A81BC06A1E"
}
```

## 错误码 { .section}

[查看本产品错误码](https://error-center.aliyun.com/status/product/DDoSPro)

