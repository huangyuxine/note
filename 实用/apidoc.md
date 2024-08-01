---
description: api注释
---

# apidoc

#### 安装 <a href="#an-zhuang" id="an-zhuang"></a>

1.安装`node.js`

2.安装`apidoc`

```
npm install apidoc -g
```

#### 配置

`apidoc.json`放在项目根目录

```json
{
  "name": "项目文档",
  "version": "0.1.0",
  "description": "项目文档描述"
}
```

#### Doc

@api {method} path \[title] 定义api

```
@api {get} /orders 订单列表
```

@apiDescription 描述

@apiGroup 接口分组

@apiVersion 版本

@apiName 名称

@apiParam 参数

```
@apiParam {String} order_no 订单号 
```

可以定义取值范围，如`{String{1..32}}`、`{String{YYYY-mm-dd}}`、`{String="0","1"}`

@apiExample 例子

```
@apiExample {url} Api Example:
/order/2015245254515158412
```

@apiSuccess 请求成功返回参数

@apiSuccessExample 成功请求返回参数

```
@apiSuccessExample {json} Success-Response:
{
      'code':200,
      'msg':'订单详情获取成功',
      'data':[]
}
```

@apiHeader 请求头

```
@apiHeader {String} access-key Users unique access-key.
```

@apiHeaderExample 请求头事例

```
@apiHeaderExample {json} Header-Example:
{
   "Accept-Encoding": "Accept-Encoding: gzip, deflate"
}
```

@apiDefine 定义公共部分，与@apiUse结合使用

```
/**
 * @apiDefine header
 * @apiHeaderExample {json} Header-Example:
 *     {
 *       "Accept-Encoding": "Accept-Encoding: gzip, deflate"
 *     }
 */
```

@apiError 错误返回参数

@apiErrorExample 错误事例

#### 综合

```
/**
 * @apiDefine header
 * @apiHeader {String} access-key Users unique access-key.
 * @apiHeaderExample {json} Header-Example:
 *     {
 *       "Accept-Encoding": "Accept-Encoding: gzip, deflate"
 *     }
 */
/**
 * @api {get} /order/:order_no 订单详情
 * @apiUse header
 * @apiPermission token
 * @apiExample {url} Api Example:
 * curl -i http://localhost/order/4711
 * @apiDescription 获取订单详情
 * @apiGroup 商品订单
 * @apiVersion 0.1.0
 * @apiName 商品详情
 * @apiParam {String} order_no 订单号
 * @apiSuccessExample {json} Success-Response:
 * {
 *         'code':200,
 *         'msg':'订单详情获取成功',
 *         'data':[]
 * }
 *
 * @apiError 10010 此订单已删除或者不存在
 * @apiErrorExample {json} Error-Response:
 *     HTTP/1.1 404 Not Found
 *     {
 *       "code": 10010,
 *       "msg":"Not Found"
 *     }
 */
```

#### 生成

把`src`目录下的文件注释生成文档

```
apidoc -i src/ -o apidoc/ 
```
