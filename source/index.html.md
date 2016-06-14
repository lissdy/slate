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

<aside class="warning">当前测试接口为公开接口，无需认证流程</aside>

# 3.业主端

## 3.1 获取报修类型

```json
{
    "result": {
        "id": "0088000I5oZvTbnqz7wkts",  //报修类型id
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

`GET http://develop.cm-inv.com/api/v1/incident_requests/categories.json`

### Input Parameters

无

## 3.2 服务单报修

```json
{
    "status_code": 200  //根据调用结果返回状态码
}
```

### HTTP请求

`POST http://develop.cm-inv.com/api/v1/incident_requests.json`

### Input Parameters

参数名称 |  描述  | 示例值
--------- | ----------- | -----------
owner_name | 业主名称| 小白
owner_phone |业主电话| 13800138000
property_number | 报修房间编号| A11-a-a-02-01
category_one | 报修一级类型id| 0088000I5oZvTbnqzM4OHI
category_two | 报修二级类型id| 0088000I5oZvTbnqzTH9km
category_three | 报修三级类型id| 0088000I5oZvTbnqzanJBY
describing | 报修问题描述| 楼道里面的灯泡爆炸咯

<aside class="notice">
若传入的业主信息不存在时，系统同步创建该业主。
</aside>


## 3.3 服务单列表

```json
{
    "status_code": 200  //根据调用结果返回状态码
}
```

### HTTP请求

`POST http://develop.cm-inv.com/api/v1/incident_requests.json`

### Input Parameters

参数名称 |  描述  | 示例值
--------- | ----------- | -----------
owner_name | 业主名称| 小白
owner_phone |业主电话| 13800138000
property_number | 报修房间编号| A11-a-a-02-01
category_one | 报修一级类型id| 0088000I5oZvTbnqzM4OHI
category_two | 报修二级类型id| 0088000I5oZvTbnqzTH9km
category_three | 报修三级类型id| 0088000I5oZvTbnqzanJBY
describing | 报修问题描述| 楼道里面的灯泡爆炸咯


## 3.4 服务单详情

```json
{
    "status_code": 200  //根据调用结果返回状态码
}
```

### HTTP请求

`GET http://develop.cm-inv.com/api/v1/incident_requests/show.json`

### Input Parameters

参数名称 |  描述  | 示例值
--------- | ----------- | -----------
request_number | 服务单号| 01201606141145





