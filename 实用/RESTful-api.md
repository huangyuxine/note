# RESTful API



{% embed url="https://wiki.kimleo.net/articles/On%20REST/" %}

{% embed url="https://phauer.com/2015/restful-api-design-best-practices/" %}

{% embed url="https://www.panghuli.cn/notebook/software-engineering/restful-api-best-practices.html#org0f7e506" %}

## 基本概念 <a href="#ji-ben-gai-nian" id="ji-ben-gai-nian"></a>

### 资源和表述

一个web服务器可以管理许多不同的URL，并且每个URL被授权访问服务器上的不同数据。我们所说的URL是一些事物的URL，它可以是产品，也可以是用户。所以说每个URL都标识了一个**资源**。

客户端向某个URL发送一条HTTP请求以后，服务器回发送一个文档（二进制文件或者是其他）作为响应，这个文档称之为**表述**。

### 可寻址性

每个URL也仅代表一个资源

### 无状态性

服务器不关心客户端的状态，比如：你访问两次URL地址，服务器不知道这两次访问来源于你

### 应用状态

比如你打开某个网站，网站里有导航，导航上每打开一个链接都是一个特定的页面，在REST中，这些信息称之为应用状态。

超媒体是应用状态的引擎，其实就是通过表单以及访问各种链接来浏览web

### 资源状态

应用状态保存在客户端，但是服务器端可以通过发送表述，也就是HTML文档来操控它。比如我们去提交了一个表单，这个表单描述了可能的状态转换。资源状态是存储在服务器端的，但是客户端可以通过向服务器发送表述，方式可能是提交一个HTML表单，这个提交的表单描述了客户端所期望的新的状态。

## HTTP协议语义

最常用的四个，其他是HEAD（轻量级版本的GET，不需要发送实体消息）、OPTIONS（获取资源所能响应的HTTP方法列表）、PATCH（类似于PUT）、LINK、UNLINK

| 动作     | 描述     |
| ------ | ------ |
| GET    | 获取资源   |
| DELETE | 删除一个资源 |
| POST   | 新增一个资源 |
| PUT    | 修改资源   |

### 幂等性

发送两次请求对资源状态的影响和发送一次请求的影响是一样的，比如DELETE，GET

## 设计指南

### 协议

采用https

### 版本号

小版本可以携带到header头中，大版本放在url里

### 路径

一般用一个名词并且是复数，来表示资源

获取用户列表：`GET https://###.com/v1/users`

新建用户：`POST https://###.com/v1/users`

删除用户：`DELETE https://###.com/v1/users/{:id}`

获取某个用户：`GET https://###.com/v1/users/{:id}`

修改某个用户：`PUT https://###.com/v1/users/{:id}`

**豆瓣为例**

| name               | Method | endpoint                        |
| ------------------ | ------ | ------------------------------- |
| \[获取图书信息]          | GET    | /v2/book/:id                    |
| \[根据 isbn 获取图书信息]  | GET    | /v2/book/isbn/:name             |
| \[搜索图书]            | GET    | /v2/book/search                 |
| \[获取某个图书中标记最多的标签]  | GET    | /v2/book/:id/tags               |
| \[获取用户对图书的所有标签]    | GET    | /v2/book/user/:name/tags        |
| \[获取某个用户的所有图书收藏信息] | GET    | /v2/book/user/:name/collections |
| \[获取用户对某本图书的收藏信息]  | GET    | /v2/book/:id/collection         |
| \[获取某个用户的所有笔记]     | GET    | /v2/book/user/:name/annotations |
| \[获取某本图书的所有笔记]     | GET    | /v2/book/:id/annotations        |
| \[获取某篇笔记的信息]       | GET    | /v2/book/annotation/:id         |
| \[获取丛书书目信息]        | GET    | /v2/book/series/:id/books       |

### 过滤

常用的有

| 参数     | 说明   |
| ------ | ---- |
| page   | 页数   |
| limit  | 条数   |
| sortby | 排序字段 |
| order  | 排序顺序 |

根据业务筛选，比如开始时间`start_time=2021-01-11`，结束时间`end_time=2021-02-11`，分类`type`等

### 状态码

http状态码

| 状态码 | 含义                    | 说明                 |
| --- | --------------------- | ------------------ |
| 200 | OK                    | 请求成功               |
| 201 | CREATED               | 创建成功               |
| 202 | ACCEPTED              | 更新成功               |
| 400 | BAD REQUEST           | 请求的地址不存在或者包含不支持的参数 |
| 401 | UNAUTHORIZED          | 未授权                |
| 403 | FORBIDDEN             | 被禁止访问              |
| 404 | NOT FOUND             | 请求的资源不存在           |
| 500 | INTERNAL SERVER ERROR | 内部错误               |

自定业务码，以豆瓣为例

| 错误码  | 错误信息                 | 含义                                 | status code |
| ---- | -------------------- | ---------------------------------- | ----------- |
| 999  | unknow\_v2\_error    | 未知错误                               | 400         |
| 1000 | need\_permission     | 需要权限                               | 403         |
| 1001 | uri\_not\_found      | 资源不存在                              | 404         |
| 1002 | missing\_args        | 参数不全                               | 400         |
| 1003 | image\_too\_large    | 上传的图片太大                            | 400         |
| 1004 | has\_ban\_word       | 输入有违禁词                             | 400         |
| 1005 | input\_too\_short    | 输入为空，或者输入字数不够                      | 400         |
| 1006 | target\_not\_fount   | 相关的对象不存在，比如回复帖子时，发现小组被删掉了          | 400         |
| 1007 | need\_captcha        | 需要验证码，验证码有误                        | 403         |
| 1008 | image\_unknow        | 不支持的图片格式                           | 400         |
| 1009 | image\_wrong\_format | 照片格式有误(仅支持 JPG,JPEG,GIF,PNG 或 BMP) | 400         |
| 1010 | image\_wrong\_ck     | 访问私有图片 ck 验证错误                     | 403         |
| 1011 | image\_ck\_expired   | 访问私有图片 ck 过期                       | 403         |
| 1012 | title\_missing       | 题目为空                               | 400         |
| 1013 | desc\_missing        | 描述为空                               | 400         |

### 请求和返回

一般为JSON格式

**获取文章列表**

```
GET https://###.com/v1/articles
{
  code:200,
  msg:"获取成功",
  data:{
    list:[{
      title:"文章1",
      content:"内容"
    },
    {
      title:"文章2",
      content:"内容"
    }],
    page:1,
    prePage:0,
    total:100,
    limit:20
  }
}
```

**业务处理失败**

```
POST https://###.com/v1/articles
{
  code:1001,
  msg:"标题不允许为空"
}
```

## 不要追求“真正的REST会怎么做”

RESTful有些理想化，对于实际的业务来说，可远比CRUD复杂的多的，如果你在实际工作中用REST有了困惑，不知道某个情况下REST此时的最佳实践是什么时，不要追求“真正的REST会怎么做”，不要被REST限制住，不要生搬硬套。
