SQL类
----
namespace: `lin\basement\sql`

包含如下类：

* **lin\basement\sql\SQLPDO**

---

### 说明
访问关系型数据库服务器，实现多节点、集群、主从，读写分离。访问模式为按读写模式的权重进行选择,不同于[KV](../kv/KV.md)的每一个操作选择不同服务器，一次实例化将**各选一个读写服务器**并固定，若是**事务，则只使用写服务器**。

---

### 功能

* basement部分，[点击查看](https://github.com/linlanye/basement)
* 扩展功能：
	* 参见[KV](../kv/KV.md)




#### 配置项

动态配置

分为两部分：

* `servers`相关部分，[点击查看](../README.md)。
* `lin`相关部分如下：

~~~php
<?php
'server'=>[
    'sql'   => [
        'use'   => '*', //使用所有服务器
        'debug' => true, //调试模式
    ],
]
~~~

#### 其他

[参见KV](../kv/KV.md)