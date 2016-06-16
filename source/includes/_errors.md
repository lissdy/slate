# Errors

<aside class="notice">众家API平台接口状态码规则同HTTP协议</aside>

以下为常用状态码说明

Error Code | Meaning
---------- | -------
200 | OK - [GET]：服务器成功返回用户请求的数据
400 | INVALID REQUEST - [POST/PUT/PATCH]：调用不合法，具体的错误信息在errmsg中进行说明，例如：“invalid code”
401 | Unauthorized -- 未认证，调用需要用户通过认证
403 | Forbidden - 访问拒绝，调用需要用户身份通过验证
404 | NOT FOUND - [*]：未找到，指定的资源不存在
500 | INTERNAL SERVER ERROR - [*]：服务器发生错误
