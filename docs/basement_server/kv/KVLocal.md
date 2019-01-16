KVLocal类
----
namespace: `lin\basement\kv`

包含如下类：

* **lin\basement\kv\KVLocal**


---

### 说明

* 使用本地文件模拟的服务器，实现高速kv服务器的操作，适用于无kv服务器环境，
功能同KV类一致([参见KVServer说明](KV.md))。

* 由于是模拟服务器，不具备[KVServer](KV.md)中罗列的方法。

* 第一次使用时会生成一个大的索引数据，消耗较大，第二次后则无此消耗。


---

### 功能

* basement部分，[点击查看](../../docs_basement/ServerKV.md)
* 扩展功能：
    * 调节模拟服务器相关参数。

---

#### 配置项

参见[KVKVServer](KV.md)

---

#### 使用

以下方法为高级使用方法，用于自定义调节模拟服务器的性能，系统已内置较优方案，不建议使用
~~~php
<?php

//关闭服务器连接
$Driver = new KVLocal;
$Driver->close();

//设置数据块参数
KVLocal::setBlockParameters(['size'=>107, 'factor'=>1.2]); //设置一个数据槽位大小为107个字节，块增长因子为1.2

//设置与最大值相关的参数
KVLocal::setMaxParameters([
    'scan_time' => 0.1, //设置线性扫描时允许执行的最大时间为0.1s
    'file_size' => 0x7fffffff, //设置一个数据文件最大为2gb
    'hash' => 85899343, //设置最大hash值为85899343（非冲突下的最大键数）
]);
~~~

---


## API

#### 列表
~~~php
//非basement部分
public function close(): bool
public static function setBlockParameters(array $params)
public static function setMaxParameters(array $params)
~~~
