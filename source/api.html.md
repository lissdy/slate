---
title: API编写规范

includes:
- errors

search: true
---

# 1.说明

物管平台API编写步骤及规范说明


# 2.API编写

物管平台API的编写分为五个步骤：

- 1、编写接口逻辑；
- 2、定义接口描述；
- 3、配置接口权限；
- 4、接口测试；
- 5、文档编写

<aside class="notice"> 本文以‘服务单报修类型接口’为例，对API的编写规范进行说明。</aside>

## 2.1 编写接口逻辑

> [jbuilder项目Github地址](https://github.com/rails/jbuilder)

> 建议以业务实体为单位创建Controller，以下代码为incident_requests_controller中获取报修类型的action

```ruby
def categories
   @categorie = Cmi::Category.roots.query_by_code("SERVICE_REQUEST_CATEGORY").first
end
```

> 创建Jbuilder模版，将接口结果以Json格式输出，以下代码为categories.json.jbuilder内容

```ruby
json.result do
  if @categorie.present?
    json.id @categorie.id
    json.name @categorie.name
    json.children @categorie.children do |child_one|
      if child_one.present?
        json.id child_one.id
        json.name child_one.name
      end
      json.children child_one.children do |child_two|
        if child_two.present?
          json.id child_two.id
          json.name child_two.name
        end
        json.children child_two.children do |child_three|
          if child_three.present?
            json.id child_three.id
            json.name child_three.name
          end
        end
      end
    end
  end
end
json.status_code 200
```


同普通业务性开发相同，接口的开发同样遵循MVC模型 ，只是在接口开发中，V层（视图）的角色由第三方承担（APP等），接口的请求入口为C层(控制)，
逻辑集中在M层（模型）。

其文档结构如下所示：

![image](/images/api-structure.png)


- 返回值：
除了返回业务请求数据之外，为了明确接口调用结果，每个接口的返回均需要附加状态码（status_code），状态码值参见errors

<aside class="warning">编写完成后须进行路由配置，注意遵循Restful风格！</aside>

## 2.2 定义接口描述

> 180_cmi_cmi/lib/api_data.rb文件中对于‘服务单报修类型接口’的描述

```ruby
  map.controller "api/v1/incident_requests", {
     :categories => {
         :name => "报修问题类型",
         :description => "返回服务单报修问题类型",
         :params => [
            {:name => :name, :classify => [:output], :type => "String", :description => "报修问题名称"},
          ]
        }
  }
```

在配置文件lib/api_data.rb中对接口进行描述，包括接口对应的controller,action,名称,描述以及输入输出参数（用于记录接口的访问日志等）。

## 2.3 配置接口权限

> 180_cmi_cmi/lib/api_data.rb文件中对于‘服务单报修类型接口’的权限配置

```ruby
:incident_request => {#指定功能编码
   :en => {:name => "Incident Api V1", :description => "Incident Api V1"},
   :zh => {:name => "服务单相关接口V1", :description => "服务单业务相关接口V1"}, #功能名称与多语言描述
   :default_flag => "N", #是否默认勾选
   :login_flag => "N", #是否必须登陆访问
   :public_flag => "Y", #是否为公开功能
   "api/v1/incident_requests" => ["categories", "..."] #定义该功能下权限
  }
```

同功能开发相同，接口同样需要在权限体系中进行管理。

参数说明：

- login_flag：为Y时，用户登录就有的功能，无需进行权限配置。（PS 这些功能又不能公开），业务开发时，此项均设置为N即可

- public_flag：为Y时，该功能为公开功能，因接口需对外发布，故接口开发中此项均为Y（通过身份验证控制接口的可访问性）

## 2.4 接口测试

> [Postman下载地址](http://www.getpostman.com/)

接口编写完成后请使用Chrome插件postman进行测试。

## 2.5 文档编写

> [Slate项目Github地址](https://github.com/lord/slate)

接口用来与外部系统进行交互，因此所有的接口必须提供相应的文档。物管平台的接口文档编写工具为slate。

<aside class="success">文档是接口最重要的部分，所有接口必须提供文档</aside>




