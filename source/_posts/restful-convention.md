---
title: RESTFul API URL 命名规范和最佳实践
date: 2021-09-30 11:18:51
tags: [RESTFul, 规范]
categories: 开发规范
---

本文收集整理了RESTFul API URL 命名规范和最佳实践，用于指导后续开发工作。

## RESTFul简述
REST 是 “表现层状态转移（Representational state transfer）”的缩写。

它意味着我们不会将真的对象通过网络发送给另一方，而只会传输对象的表现层状态。而表现层状态通常是通过JSON、XML、HTML或者文本等其他格式传输的。

我们通过使用无状态协议和标准REST操作（比如GET, HEAD, POST, PUT, PATCH, DELETE, CONNECT, OPTIONS 和 TRACE）来创建高性能、可靠的微服务或API。

## 资源为核心
所有的REST原则和架构都以**资源（Resource）**为核心。通过一系列架构风格定义了创建API/服务/微服务的范例。
任何一个REST URI都应该遵循一套语义规则、准确定义资源位置，使得其他人可以通过网络访问该资源。
下面是一些最佳实践。

## URL 结构 及 API URL 命名规范
### URL 要有较高的可读性 
好的URL具有统一性，能够让大家很容易发现和理解

下面是一个比较好的 URL 示例:
```
https://api.thecodebuzz.com/v1.0/orders/{order-id}
```
下面是一个不太友好的 URL 示例:
```
https://api.thecodebuzz.com/GetOrder/OMS/BDS/{order-id}
```
我们接下来详细分析一下URL的命名。

### URL中应使用名词，避免使用CURD等动词
URL应该只包含名词而不应该使用向GetData、UpdateData这样的动词。

不建议采用的URL:
```
https://api.thecodebuzz.com/v1.0/GetOrders/{order-id}

https://api.thecodebuzz.com/v1.0/DeleteOrders/{order-id}
```

推荐的URL：
```
https://api.thecodebuzz.com/v1.0/orders/{order-id}

https://api.thecodebuzz.com/v1.0/orders/{order-id}
```

请注意上面的推荐的例子中，查询订单和删除订单的URL是一样的。这是因为这两个URL都操作了同一个资源，而查询和删除的操作是通过标准REST操作来区分的，如果我们使用curl程序来调用接口，那么查询、修改和删除操作的命令如下所示：

```
curl GET https://api.thecodebuzz.com/v1.0/orders/123

curl PUT https://api.thecodebuzz.com/v1.0/orders/123

curl DELETE https://api.thecodebuzz.com/v1.0/orders/123
```

### 使用名词复数
地址中推荐使用名词复数

示例：
```
https://api.thecodebuzz.com/v1.0/orders/{order-id}

https://api.thecodebuzz.com/v1.0/payments/{pay-id}
```

### URL中使用短横线 (-) 
与下划线等其他分隔符相比，短横线（-）更能提升可读性。如果遇到较长的名字，请使用短横。
示例：
```
https://api.thecodebuzz.com/v1.0/books-management/north-america/orders/{order-id}

https://api.thecodebuzz.com/v1.0/books-management/north-america/payments/{payment-id}
```

### URL中不应使用下划线(_)
下划线可读性不佳，经常跟其他行混淆；

一些DNS不支持含有下划线的URL；

为了避免不必要的冲突，如果遇到较长的名字，请使用短横线。

不建议的URL：
```
https://api.thecodebuzz.com/v1.0/books_management/north_america/orders/{order-id}
```

推荐：
```
https://api.thecodebuzz.com/v1.0/books-management/north-america/orders/{order-id}
```

### API URL 长度限制
As per RFC 7230,
按照RFC 7230的要求，

> HTTP请求中不存在预先定义的长度上限。[...] 服务器收到超过预期长度上限的请求必须回复“414(URI Too Long)”状态码。

另一方面，现有部分服务确实无法处理超过2048个字符长度的URL。

URL长度超长通常伴随着含有较多参数的HTTP GET方法出现，需要引起注意。

## 更多示例
### HTTP GET

```
//获取所有书籍
HTTP GET  http://api.thecodebuzz.com/books-agencies/agencies/books

//获取某一出版社的书籍
HTTP GET  http://api.thecodebuzz.com/books-agencies/agencies/{agency-id}/books

//获取某一出版社的特定书籍
HTTP GET  http://api.thecodebuzz.com/books-agencies/agencies/{agency-id}/books/{book-id}
```

### HTTP POST
```
//新增一本书
http://api.thecodebuzz.com/books-agencies/agencies/books  
```

### HTTP PUT

```
//更新所有出版社的某一本书
HTTP PUT http://api.thecodebuzz.com/books-agencies/agencies/books/{book-id} 
```

### HTTP DELETE
```
//删除所有出版社的同一本书
HTTP DELETE http://api.thecodebuzz.com/books-agencies/agencies/books/{book-id}  
//删除指定出版社的指定书籍
HTTP DELETE http://api.thecodebuzz.com/books-agencies/agencies/{agency-id}/books/{book-id}  
```

上面示例中的命名都遵循了通用的规范，可以很容易的用来构建新的RESTFul服务。

## 总结
资源是REST理念、标准和架构的核心。基于REST的URI应该遵循相应的语义规则和标准来标识API中的资源。亲爱的读者如果有任何的问题，欢迎在下方留言。