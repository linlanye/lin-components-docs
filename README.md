# Lin/1.0 说明文档

## 介绍

本文档为php框架[Lin](https://github.com/linlanye/lin)所使用的组件[Lin-components](https://github.com/linlanye/lin-components)的说明文档。文档内容分为以下三个部分：

* [basement一般组件](basement/README.md)
* [basement的服务器组件](basement_server/README.md)
* [一般组件](components/README.md)

（basement项目参见：[https://github.com/linlanye/basement](https://github.com/linlanye/basement)）


## 组件列表

#### [Basement](basement/README.md)

* [Config](basement/config/Config.md) （配置类）
* [Debug](basement/debug/Debug.md)	  （调试类）
* [Event](basement/event/Event.md)    （事件类）
* [GeneralException](basement/exception/GeneralException.md) （扩展异常类）
* [Lang](basement/lang/Lang.md) （语言映射类）
* [Log](basement/log/Log.md) （日志类）
* [Request](basement/request/Request.md) (请求类)

#### [Basement Server](basement_server/README.md)

* [KV](basement_server/kv/KV.md) （Key-Value型服务器操作类）
* [KVLocal](basement_server/kv/KVLocal.md) （Key-Value型模拟服务器操作类）
* [Local](basement_server/local/Local.md)  （本地文件操作类）
* [Queue](basement_server/queue/Queue.md)   （队列型服务器操作类）
* [QueueLocal](basement_server/queue/QueueLocal.md) （模拟队列型服务器操作类）
* [SQL](basement_server/sql/SQLPDO.md) （关系数据库操作类）

#### [Components](components/README.md)

* [LSE](components/algorithms/LSE.md) （对称加密类）
* [Exception](components/exception/Exception.md) （自定义异常类）
* [Layer](components/layer/Layer.md) （架构类，见[LBA说明](https://github.com/linlanye/lin)）
* [Model](components/orm/Model.md) （数据模型类）
* [Query](components/orm/Query.md) （数据查询类）
* [Formatter](components/processor/Formatter.md) （数据格式化类）
* [Mapper](components/processor/Mapper.md) （数据映射类）
* [Validator](components/validator/Validator.md) （数据校验类）
* [Response](components/response/Response.md) （响应类）
* [Route](components/route/Route.md) （路由类）
* [Security](components/security/Security.md) （安全类）
* [URL](components/url/URL.md) （url构建类）
* [View](components/view/View.md) （视图类）

## 捐赠
![捐赠林澜叶](https://img.lin-php.com/donations.png)

## 版权信息
* 作者：林澜叶(linlanye)版权所有。
* 开源协议：[Apache-2.0](LICENSE)

