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

**说明：此接口为客户端与平台交互的初始接口，故在该接口的调用时，需传入用户身份信息（姓名、电话）进行模拟登录，以便获取随后进行身份标识的token值。**

根据业主信息查询所属服务单

```json
{
  "result": {
    "incident_requests": [
      {
        "request_number": "01201606213389",
        "node": "花园小区3号楼1单元2层202室",
        "contact_person": "小西",
        "category": "楼道=>灯泡=>爆炸",
        "request_time": "2016-06-22T16:23:07+08:00"  //提单时间
      },
      {
        "request_number": "01201606214663",
        "node": "花园小区3号楼3单元1层101室",
        "contact_person": "小西",
        "category": "楼道=>灯泡=>爆炸",
        "request_time": "2016-06-21T14:23:07+08:00"  //提单时间
      }
    ]
  },
  "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJicF9pZCI6IjAwNVkwMDBBMHNUZFlReGZTZzl4T2kifQ.",
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
incident_status |请求的服务单状态| PENDING


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
property_number | 报修房间编号（必输）| A11-a-a-02-01
category_one | 报修一级类型id（必输）| 0088000I5oZvTbnqzM4OHI
category_two | 报修二级类型id（必输）| 0088000I5oZvTbnqzTH9km
category_three | 报修三级类型id（必输）| 0088000I5oZvTbnqzanJBY
appoint_time | 预约时间| 2016-06-22 18:39:00(注意时间格式)
describing | 报修问题描述| 小西在报修哦
repairpic | 维修前图片| 上传维修前图片（需在前端控制上传图片类型，大小）



## 4.4 服务单详情

```json
{
  "result": {
    "request_number": "01201606214663",  //服务单号
    "request_time": "2016-05-30 15:30", //报修时间
    "category": "楼道=>灯泡=>爆炸", 
    "contact": "小西",  //联系人
    "contact_phone": "15800703186",  //联系人电话
    "incident_status": "新建",  //状态
    "start_time": "2016-06-01 11:00",  //服务开始时间
    "end_time": "2016-06-01 11:30",  //服务结束时间
    "fee": "人工费-40",
    "material_fee": "1320.0", //物料价格
    "describing": "小西在报修",  //问题描述
    "repairpic": "/repairpics/small/missing.png",  //维修前图片
    "repairpicend": "/repairpicends/small/missing.png"  //维修后图片
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
request_number | 服务单号| 01201606214663


# 5.物业端

## 5.1 服务单列表

**说明：此接口为客户端与平台交互的初始接口，故在该接口的调用时，需传入用户身份信息（姓名、电话），以便获取随后进行身份标识的token值。**

根据物业人员信息查询所属服务单

```json
{
  "result": {
    "incident_requests": [
      {
        "request_number=": "01201606213389",
        "node=": "花园小区3号楼1单元2层202室",
        "contact_person=": "小西",
        "category=": "楼道=>灯泡=>爆炸"
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
incident_status |请求的服务单状态| DONE


### incident_status value

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

`POST http://develop.cm-inv.com/api/v1/incident_requests/status`

### Input Parameters

参数名称 |  描述  | 示例值
--------- | ----------- | -----------
request_number | 服务单编号|01201606279842
status_action | 针对服务单的操作动作| DONE确认完成

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

## 5.6 创建维修记录
```json
//传入服务单编号不存在或结算方参数未传递时
{
  "status_code": 400,
  "errmsg": "invalid params"
}
//创建成功返回
{
  "status_code": 200
}
```
创建维修记录
### HTTP请求
*HTTP头附加token信息*

`POST http://develop.cm-inv.com/api/v1/incident_requests/repair`

### Input Parameters
参数名称 |  描述  | 示例值
--------- | ----------- | -----------
request_number | 服务单编号(必输)| 01201606165729
pay_party | 结算方code(必输)| TENANT_SETT
settlement| 是否现场结算（Y为是，N为否）| Y
repair_reason| 故障原因维修记录|换了个马桶
add_explain| 备注| 孔大爷很满意
repairpicend| 维修后照片| 上传维修后照片

## 5.7 物料搜索
```json
{
  "result": {
	"material": [
		{
		"material_name": "消毒液11",
		"material_number": "item00130716",
		"sh_name": "青年汇仓库001",
        "sh_number": "XXX",  #仓库编号
        "material_price": "100", #物料价格
		"material_count": 24
		}
	  ]
    },
   "status_code": 200
}
```

对指定项目内的库存按照名称进行模糊查询
### HTTP请求
*HTTP头附加token信息*

`GET http://develop.cm-inv.com/api/v1/wm_materials/search`

### Input Parameters
参数名称 |  描述  | 示例值
--------- | ----------- | -----------
request_number | 服务单编号 *| 01201606233963
material_name | 物料名称 *| 消毒液


## 5.8 物料申请
客户端确定批量创建的物料集合后，调用此接口，创建物料申请单（同步申请物料）。

### HTTP请求
*HTTP头附加token信息*

> 传入参数示例

```json
{
  "request_number": 01201606213748,
  "application_form": [
    {
    "sh_number": "QNHWM001",   //仓库编码
    "material_number": "item00130716",  //物料编码,
    "count": "34"  //数量
    },
    {
    "sh_number": "QNHWM002",   //仓库编码
    "material_number": "item00130714",  //物料编码,
    "count": "4"  //数量
    }
    ]
}
```

> 返回结果

```json
//创建成功返回
{
  "status_code": 200
}
```


`POST http://develop.cm-inv.com/api/v1/wm_materials/create_application`

### Input Parameters
参数名称 |  描述  | 示例值
--------- | ----------- | -----------
request_number | 服务单编号| 01201606213748
application_form | 物料集合| 待申请物料集合


## 5.9 物料签收

客户端确定需要签收的物料集合后，调用此接口，进行物料签收。

### HTTP请求
*HTTP头附加token信息*

> 传入参数示例（同物料申请）

```json
{
  "request_number": 01201606213748,
  "sign_form": [
    {
    "sh_number": "QNHWM001",   //仓库编码
    "material_number": "item00130716",  //物料编码,
    "count": "34"  //数量
    },
    {
    "sh_number": "QNHWM002",   //仓库编码
    "material_number": "item00130714",  //物料编码,
    "count": "4"  //数量
    }
    ]
}
```

> 返回结果

```json
//创建成功返回
{
  "status_code": 200
}
```


`POST http://develop.cm-inv.com/api/v1/wm_materials/sign`

### Input Parameters
参数名称 |  描述  | 示例值
--------- | ----------- | -----------
request_number | 服务单编号| 01201606213748
sign_form | 物料集合| 待签收物料集合


## 5.10 费项条目列表

获取费项条目

### HTTP请求
*HTTP头附加token信息*

> 返回结果

```json
{
  "result": [
    {
    "sh_number": "QNHWM001",   //仓库编码
    "material_number": "item00130716",  //物料编码,
    "count": "34"  //数量
    },
    {
    "sh_number": "QNHWM002",   //仓库编码
    "material_number": "item00130714",  //物料编码,
    "count": "4"  //数量
    }
    ],
  "status_code": 200
}
```

`GET http://develop.cm-inv.com/api/v1/wm_materials`

### Input Parameters
参数名称 |  描述  | 示例值
--------- | ----------- | -----------
request_number | 服务单编号| 01201606213748


## 5.11 物料列表

根据服务单编号获取物料

### HTTP请求
*HTTP头附加token信息*

> 返回结果

```json
{
  "result": {
    "request_materials": [
      {
        "material_number": "abc00000004",  //物料名称
        "spare_part_price": "23.0",  //价格
        "spare_part_number": 10,  //申请数量
        "total_count": "3000.0"  //库存数量
      },
      {
        "material_number": "abc00000003",
        "spare_part_price": "10.0",
        "spare_part_number": 10,
        "total_count": "1988.0"
      },
      {
        "material_number": "abc00000004",
        "spare_part_price": "23.0",
        "spare_part_number": 20,
        "total_count": "3000.0"
      },
      {
        "material_number": "abc00000004",
        "spare_part_price": "23.0",
        "spare_part_number": 13,
        "total_count": "3000.0"
      }
    ]
  },
  "status_code": 200
}
```

`GET http://develop.cm-inv.com/api/v1/wm_materials/request_material_index`

### Input Parameters
参数名称 |  描述  | 示例值
--------- | ----------- | -----------
request_number | 服务单编号| 01201606247586



## 5.12 获取费项条目

获取服务单相关费项

### HTTP请求
*HTTP头附加token信息*

> 返回结果示例

```json
{
  "result": {
    "fees": [
      {
        "fee_id": "006I000P1OGHNdEZeHmQts",
        "fee_name": "疏通下水道",
        "repair_fee": "70.0"
      },
      {
        "fee_id": "006I000P1OGHNdEZeWqdE0",
        "fee_name": "水电表修理",
        "repair_fee": "40.0"
      },
      {
        "fee_id": "006I000P1OGdMJW9h3qw2y",
        "fee_name": "门锁维护",
        "repair_fee": "100.0"
      },
      {
        "fee_id": "006I000A0tDin5qcq6M7hA",
        "fee_name": "道路损坏",
        "repair_fee": "4000.0"
      },
      {
        "fee_id": "006I000P1OGdMJW9h3qw2y",
        "fee_name": "门锁维护",
        "repair_fee": "50.0"
      },
      {
        "fee_id": "006I000P1OGHNdEZeHmQts",
        "fee_name": "疏通下水道",
        "repair_fee": "45.0"
      },
      {
        "fee_id": "006I000P1OGHNdEZeWqdE0",
        "fee_name": "水电表修理",
        "repair_fee": "20.0"
      }
    ]
  },
  "status_code": 200
}
```

`GET http://develop.cm-inv.com/api/v1/fees/items`

### Input Parameters

无


## 5.13 保存费项信息

获取服务单费用相关信息

### HTTP请求
*HTTP头附加token信息*

> 返回结果示例

```json
{
  "status_code": 200
}
```

`POST http://develop.cm-inv.com/api/v1/fees`

### Input Parameters
参数名称 |  描述  | 示例值
--------- | ----------- | -----------
request_number | 服务单编号| 01201606213748
fee_id | 费项对应id| 006I000P1OGdMJW9h3qw2y
repair_fee | 费项对应价格| 30
material_fees | 物料费用集合| 暂无格式，暂空








