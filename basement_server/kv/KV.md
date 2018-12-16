KV类
----
namespace: `lin\basement\kv`

包含如下类：

* **lin\basement\kv\KVLocal** ([参见KVLocal说明](KVLocal.md))
* **lin\basement\kv\KVMemcache**
* **lin\basement\kv\KVMemcached**
* **lin\basement\kv\KVRedis**

---

### 说明

KV类用于操作Key-Value型服务器，全部实现集群访问。

* `lin\basement\kv\KVMemcache` 用于**windows**下访问memcached服务器，由memcache扩展的addServer()方法添加多个服务器到服务器连接池。
* `lin\basement\kv\KVMemcached` 用于**unix**下访问memcached服务器，由memcache扩展的addServers()方法添加多个服务器到服务器连接池。
* `lin\basement\kv\KVRedis` 用于访问redis服务器，由lin自身实现的一致性哈希算法实现集群访问，并在实例每一次操作的时候散列到对应的服务器。



---

### 功能

* basement部分，[点击查看](https://github.com/linlanye/basement)
* 扩展功能：
	* 采用一致性哈希算法操作服务器集群。
	* 获得当前使用的服务器索引号、当前的服务器驱动、当前的服务器类型名。



#### 配置项

动态配置

分为两部分：

* `servers`相关部分，[点击查看](../README.md)。
* `lin`相关部分如下：

~~~php
<?php

'server'=>[
	'kv'    => [
	    'driver'  => [ //可使用的不同驱动配置
	        'memcached' => [ //使用memcached服务器
	            'use' => '*', //使用所有服务器
	        ],
	        'redis'     => [ //使用redis服务器
	            'use' => 'kv*', //使用所有kv标识的服务器
	        ],
	        'local'     => [ //使用本地模拟服务器的配置
	            'path' =>  'data/kv', //模拟数据存放路径
	        ],
	    ],
	    'prefix'  => '', //键前缀
	    'default' => [
	        'life' => 3600 * 24 * 7, //默认的缓存时间，单位s
	    ],
	    'debug'   => true, //调试模式
	]
]

~~~

#### 使用

~~~php
<?php
//实例化服务器KVmemcache和KVRedis一致
$Driver = new KVMemcached; //自动选择服务器
$Driver = new KVMemcached($Driver); //使用外部指定的驱动实例
$Driver = new KVMemcached(1); //使用索引为1的服务器，lin-servers.php文件中存在该索引的配置

//设置当前实例的键前缀
$Driver->setPrefix('lin'); //设置key的前缀为lin

//获得当前使用的服务器索引
$Driver->getIndex();

//获得当前使用的驱动实例
$Driver->getDriver();

//获得当前服务器名，memcached或redis
$Driver->getServerName();

~~~


---


## API

#### 列表
~~~php
//非basement部分
public function __construct($DriverOrIndex = null)
public function getIndex():  ? string
public function getDriver() :  ? object
public function getServerName():  ? string
public function setPrefix(string $prefix): bool
~~~

#### 详细说明

**__construct($DriverOrIndex = null)**: 实例化服务器，并设置自动选择或手动指定
```php
params:
    object|string $DriverOrIndex=null 为可访问的服务器驱动对象时（满足相应的Server规范），则实例使用该对象访问服务器；为字符串时，则作为索引，调用servers.php配置文件里的对应索引的配置实例化
```

**getIndex()**: 获得使用的索引，若传入的是外部指定的驱动，则返回'user'，否则返回当前使用的服务器索引号。
```php
params:
	void
return:
	string|null 失败或未使用返回null
```

**getDriver()**: 获得当前使用的服务器驱对象。
```php
params:
	void
return:
	object|null 失败或未使用返回null
```

**getServerName()**: 获得当前服务器名，memcached或redis
```php
params:
	void
return:
	string|null 使用memcached则返回memcached，使用redis则返回redis。失败或未使用返回null
```

**setPrefix()**: 设置当前键前缀
```php
params:
	string $prefix 键前缀
return:
	bool 是否成功
```