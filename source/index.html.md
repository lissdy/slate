---
title: API Reference

includes:
  - errors

search: true
---

# 1.说明

中民众家平台API说明文档

# 2.认证

平台使用Oauth2.0认证协议，调用分为三个步骤：获取access_token、使用access_token调用接口、access_token有效期过期后刷新access_token。

access_token是HI平台的全局唯一票据，第三方调用各接口时都需使用access_token。请进行妥善保存。access_token的存储至少要保留512个字符空间。access_token的有效期目前为2小时，需定时刷新，重复获取将导致上次获取的access_token失效。

<aside class="success">当前测试接口为公开接口，无需认证流程</aside>

# 3.身份验证

平台采用基于toke(**注意与Oauth2中的access-token区分**)的身份验证，第三方与平台第一次交互过程中，带入用户身份信息（业主：姓名／手机号），若请求成功，平台返回token值。
第三方需将token值保存至本地，并在随后的请求中将token值附入HTTP请求头中，用于用户身份识别。

业主端：由于用户没有登出操作，因此采用token定时（2小时）失效的方式来保证安全性，若失效后，需重新获取token。

> [参考资料：http://ninghao.net/blog/2834](http://ninghao.net/blog/2834)

<aside class="warning">若token认证失败，状态码：403</aside>

# 4.业主端

## 4.1 服务单列表

**说明：此接口为客户端与平台交互的初始接口，故在该接口的调用时，需传入用户身份信息（姓名、电话），以便获取随后进行身份标识的token值。**

根据业主信息查询所属服务单

```json
{
  "result": {
    "incident_requests": [
      {
        "request_number=": "01201606161281",
        "contact_person=": "小西",
         //......
      }
    ]
  },
   //后续请求赋在HTTP头中
  "token": "yJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJicF9pZCI6IjAwNVkwMDA5MHZxNTNSaEZYMWE5MzIifQ.",
  "status_code": "200"
}
```

### HTTP请求

`GET http://develop.cm-inv.com/api/v1/incident_requests`

### Input Parameters

参数名称 |  描述  | 示例值
--------- | ----------- | -----------
role | 用户角色，在业主端调用时，该值为OWNER | OWNER
user_name | 业主名称| 小西
user_phone |业主电话| 15800703186
status |请求的服务单状态| DONE


### status value

code |  描述
--------- | -----------
DONE | 已完结
PENDING |处理中


## 4.2 获取报修类型

```json
{
  "result": {
    "id": "0088000I5oZvTbnqz7wkts",
    "name": "服务单三级类型",
    "children": [
      {
        "id": "0088000I5oZvTbnqzM4OHI",
        "name": "楼道",
        "children": [
          {
            "id": "0088000I5oZvTbnqzTH9km",
            "name": "灯泡",
            "children": [
              {
                "id": "0088000I5oZvTbnqzanJBY",
                "name": "爆炸"
              }
            ]
          }
        ]
      }
    ]
  },
  "status_code": 200
}
```
获取服务单三级报修类型

### HTTP请求
*HTTP头附加token信息*

`GET http://develop.cm-inv.com/api/v1/incident_requests/categories`

### Input Parameters

无

## 4.3 服务单报修

```json
{
    "status_code": 200  //根据调用结果返回状态码
}
```

### HTTP请求
*HTTP头附加token信息*

`POST http://develop.cm-inv.com/api/v1/incident_requests`



### Input Parameters

参数名称 |  描述  | 示例值
--------- | ----------- | -----------
property_number | 报修房间编号| A11-a-a-02-01
category_one | 报修一级类型id| 0088000I5oZvTbnqzM4OHI
category_two | 报修二级类型id| 0088000I5oZvTbnqzTH9km
category_three | 报修三级类型id| 0088000I5oZvTbnqzanJBY
describing | 报修问题描述| 小西在报修哦


## 4.4 服务单详情

```json
{
    "result": {
        "request_number=": "01201606146471"
         ...... //服务单信息
    },
    "status_code": 200
}
```

### HTTP请求
*HTTP头附加token信息*

`GET http://develop.cm-inv.com/api/v1/incident_requests/show`

### Input Parameters

参数名称 |  描述  | 示例值
--------- | ----------- | -----------
request_number | 服务单号| 01201606146471


# 5.物业端

## 5.1 服务单列表

**说明：此接口为客户端与平台交互的初始接口，故在该接口的调用时，需传入用户身份信息（姓名、电话），以便获取随后进行身份标识的token值。**

根据物业人员信息查询所属服务单

```json
{
  "result": {
    "incident_requests": [
      {
        "request_number=": "01201606161281",
        "contact_person=": "孔大娘",
         //......
      }
    ]
  },
   //后续请求赋在HTTP头中
  "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJicF9pZCI6IjAwNVkwMDA5MHZxNTNSaEZYMWE5MzIifQ.",
  "status_code": "200"
}
```

### HTTP请求

`GET http://develop.cm-inv.com/api/v1/incident_requests`

### Input Parameters

参数名称 |  描述  | 示例值
--------- | ----------- | -----------
role | 用户角色，在物业端调用时，该值为STAFF | STAFF
user_name | 物业人员名称| 孔老师
user_phone |物业人员电话| 15284413186
status |请求的服务单状态| DONE


### status value

code |  描述
--------- | -----------
NEW_ASSIGN | 待办
SOURCE | 发起
DONE | 经办

## 5.2 报修节点

```json
//无参数时，返回当前物业人员所辖小区列表
{
  "result": {
    "cascade_nodes": [
      {
        "id": "006i000A0ruXeusRVEbFaq",
        "structure_name": "花园小区"
      },
      {
        "id": "006i000P1O8ppJGdzz4oQi",
        "structure_name": "青年汇"
      },
      {
        "id": "006i000P1OFDRaNLKrmjqa",
        "structure_name": "怡海花园"
      }
    ]
  },
  "status_code": 200
}
//"传入parent_id：'006i000A0ruXeusRVEbFaq'查询花园小区下属结构"
{
  "result": {
    "cascade_nodes": [
      {
        "id": "006i000A0ruBgEarUOOAfQ",
        "structure_name": "1号楼"
      },
      {
        "id": "006i000A0ruBgEarUPe264",
        "structure_name": "2号楼"
      },
      {
        "id": "006i000A0ruBgEarUQvJ9U",
        "structure_name": "3号楼"
      }
    ]
  },
  "status_code": 200
}
```
获取报修节点

### HTTP请求
*HTTP头附加token信息*

`GET http://develop.cm-inv.com/api/v1/structures/cascade_nodes`

### Input Parameters

无或parent_id

参数名称 |  描述  | 示例值
--------- | ----------- | -----------
parent_id | 父级建筑结构id | 006i000A0ruXeusRVEbFaq(花园小区)


## 5.3 服务单报修

> 同业主端[服务单报修](#4-3)


## 5.4 服务单状态变更


```json
//'传入服务单编号不存在或不合法的action'
{
  "status_code": 400,
  "errmsg": "invalid params"
}
//'状态成功变更或时间成功记录'
{
  "status_code": 200
}
```


针对服务单的‘开始接单’，‘开始维修’，‘确认完成’三个动作进行状态变迁或活动时间纪录。

### HTTP请求
*HTTP头附加token信息*

`PUT http://develop.cm-inv.com/api/v1/incident_requests/:id(附服务单编号)`

### Input Parameters

参数名称 |  描述  | 示例值
--------- | ----------- | -----------
action | 针对服务单的操作动作| DONE确认完成

### action value
code |  描述
--------- | -----------
DONE | 确认完成
RECEIVE |接单
REPAIR |维修


## 5.5 获取结算方
```json
{
  "result": {
    "pay_parts": [
      {
        "name": "业主结算",
        "code": "OWNER_SETT"
      },
      {
        "name": "物业结算",
        "code": "TENANT_SETT"
      },
      {
        "name": "公共维修基金结算",
        "code": "ALL_SETTLE"
      },
      {
        "name": "其他",
        "code": "OTHER_SETT"
      }
    ]
  },
  "status_code": 200
}
```


获取维修记录中的结算方列表
### HTTP请求
*HTTP头附加token信息*

`GET http://develop.cm-inv.com/api/v1/incident_requests/pay_parts`

### Input Parameters
无





