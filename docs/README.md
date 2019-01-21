# Lin1.0 说明文档
[![Latest Stable Version](https://poser.pugx.org/lin/lin/v/stable)](https://packagist.org/packages/lin/lin)
[![Total Downloads](https://poser.pugx.org/lin/lin/downloads)](https://packagist.org/packages/lin/lin)
[![PHP Version](https://img.shields.io/badge/php-%3E%3D7.2-8892BF.svg)](http://www.php.net/)
[![License](https://poser.pugx.org/lin/lin/license)](https://packagist.org/packages/lin/lin)
---


## 介绍

**Lin是**一套基于[php7.2](http://www.php.net)的全新web框架，它具有一套全组件化的开发理念，避免了以往web框架可定制性差、扩展开发繁杂、布局耦合度较高等缺点，完美实现了三重分离：应用层、框架层、组件层。使用者只需通过堆积木形式将一个个功能进行组装即可，而无需花费大量精力去理解一个框架的核心理念。Lin解耦了绝大多数开发场景，让协同开发更为简单，并且应用结构从一开始就基于高度弹性化的架构模式，对后续扩展、维护、升级都可以0成本轻松实现。

## 特性

* 全组件化，框架运行流程完全由使用者自行控制，通过一个个组件堆积而成。
* 自带模拟kv、queue服务器，无需安装memcache和redis等外部环境，并可轻松一键切换。
* 原生透明化支持SQL、Key-Value、Queue等服务器主从、多节点访问。
* 新的组织架构[LBA](framework/LBA.md)，解决传统MVC模式的短板，可快速对应用架构轻量化弹性升级。
* 涵盖web开发的绝大多数场景，组件功能接口简单，学习接近0成本。
* 生产环境和开发环境无缝替换，生产部署极致简单。
* 应用层、框架层、组件层三重分离，解耦框架与应用直接的强耦合。
* 基于组件通用化项目[basement](basement/README.md)，开发更加透明化、组件调用更加规范化。
* 接口风格高度一致的极简主义设计，避免所谓“优雅的语法”带来的繁杂的命名风格。
* 路由、安全、数据等相关功能高度封装，极大降低应用开发中出现安全漏洞的风险。


## 安装

* composer 方式
```
composer create-project lin/lin
```

* 下载压缩文件

[https://downloads.lin-php.com/lin-1.0.0.zip](https://downloads.lin-php.com/lin-1.0.0.zip)


## 快速使用

访问`public/index.php`即可，无论`public`目录是否处于web根目录、http服务器是否配置rewrite皆不影响应用的开发和运行。


## 组件列表

### [Basement](basement/README.md)

* [Config](basement/config/Config.md) （配置类）
* [Debug](basement/debug/Debug.md)      （调试类）
* [Event](basement/event/Event.md)    （事件类）
* [Exception](basement/exception/GeneralException.md) （扩展异常类）
* [Lang](basement/lang/Lang.md) （语言映射类）
* [Log](basement/log/Log.md) （日志类）
* [Request](basement/request/Request.md) (请求类)

### [Basement Server](basement_server/README.md)

* KV（Key-Value型服务器操作类）
    * [KVServer](basement_server/kv/KV.md)（远程服务器）
    * [KVLocal](basement_server/kv/KVLocal.md)（模拟服务器）
* Queue（队列型服务器操作类）
    * [QueueServer](basement_server/queue/Queue.md)（远程服务器）
    * [QueueLocal](basement_server/queue/QueueLocal.md)（模拟服务器）
* [Local](basement_server/local/Local.md)（本地文件操作类）
* [SQL](basement_server/sql/SQLPDO.md)（关系数据库操作类）

### [Components](components/README.md)

* [LSE](components/algorithms/LSE.md) （对称加密类）
* [Exception](components/exception/Exception.md) （自定义异常类）
* [Layer](components/layer/Layer.md) （架构类，见[LBA说明](framework/LBA.md)）
* ORM（对象关系映射类）
    * [Model](components/orm/Model.md) （模型）
    * [Query](components/orm/Query.md) （查询）
* Processor（数据处理类）
    * [Formatter](components/processor/Formatter.md) （格式化）
    * [Mapper](components/processor/Mapper.md) （映射）
* [Validator](components/validator/Validator.md) （数据校验类）
* [Response](components/response/Response.md) （响应类）
* [Route](components/route/Route.md) （路由类）
* [Security](components/security/Security.md) （安全类）
* [URL](components/url/URL.md) （url构建类）
* [View](components/view/View.md) （视图类）



## 约定
对本文档一些术语进行解释，并在整个说明文档中都保持一致的意思。

* **参见basement**：由于部分接口函数符合[basement](https://github.com/linlanye/basement)的规范要求，所以可直接前往[basement项目](docs_basement/README.md)查看具体说明。
* **动、静态配置**：每个组件类都依赖于一个专用的配置内容。有些类的配置项在整个生命期只加载一次，后续调整配置内容不会产生变化，称为静态配置；有些类的配置项则在每一次实例化都会重新加载，称为动态配置。
* **重置**：有些类会提供`reset()`函数，一般代表重置的意思。重置不仅清空一切数据，还清空配置项，在这种情况下，再次运行静态配置的类会重新加载配置项。
而`clean()`函数一般只清空数据，不清空配置项。
*  **设置**：用于替代构造函数，方法名为`setting()`,在该方法中书写实例化时的前置执行，具有该方法的类其构造函数不可被覆写。

## 捐赠
![捐赠林澜叶](https://img.lin-php.com/donations.png)

## 版权信息
* 作者：林澜叶(linlanye)版权所有。
* 开源协议：[Apache-2.0](https://github.com/linlanye/lin/blob/HEAD/LICENSE)