此部分为Lin的[basement](https://github.com/linlanye/basement) 服务器操作部分组件
---

### 说明

所有服务器访问都实现了多节点、集群的透明化访问，仅仅通过配置文件里增加服务器的地址信息和相关模式即可实现多服务器访问。

#### 包含如下：
---

* [KVLocal](basement_server/kv/KVLocal.md) （Key-Value型模拟服务器操作类）
```
lin\basement\server\kv\KVLocal;
```

* [KV](basement_server/kv/KV.md) （Key-Value型服务器操作类）
```
lin\basement\server\kv\KVMemecache;  //windows下操作memcached
lin\basement\server\kv\KVMemecached; //linux下操作memcached
lin\basement\server\kv\KVRedis;      //操作redis
```

* [Local](basement_server/local/Local.md)  （本地文件操作类）
```
lin\basement\server\local\Local;
```

* [QueueLocal](basement_server/queue/QueueLocal.md)   （模拟队列型服务器操作类）
```
lin\basement\server\queue\QueueLocal;
```

* [Queue](basement_server/queue/Queue.md)   （队列型服务器操作类）
```
lin\basement\server\queue\QueueRedis;
```

* [SQL](basement_server/sql/SQLPDO.md) （关系数据库操作类）
```
lin\basement\server\sql\SQLPDO;
```

### 配置项

所有服务器组件皆为`动态配置`，使用两个配置文件。

* servers配置文件，参考如下：

~~~php
<?php

//配置形如
[
	'服务器名1'=>[
		'服务器索引1'=>['服务器配置'],
		...
	],
	...
]

//sql服务器，具有索引为r和w的两个服务器
'sql'       => [
    'r' => ['dsn' => 'mysql:host=127.0.0.1;port=3306;dbname=test;charset=utf8', //不同sql产品的dsn参考pdo说明
        'user'        => 'root', //连接账号
        'pwd'         => 'root', //连接密码
        'weight'      => 99, //权重，多个数据服务器存在时有用,不同模式下权重分开计算
        'mode'        => 'r', //访问模式,r为只读,w为只写,其余为读写,分别设置两个便可实现读写分离
    ],
    'w' => ['dsn' => 'mysql:host=127.0.0.1;port=3306;dbname=test;charset=utf8',
        'user'        => 'root',
        'pwd'         => 'root',
        'weight'      => 99,
        'mode'        => 'w',
    ],
],

//memcached服务器，具有索引为0和1的两个服务器
'memcached' => [
    ['host' => '127.0.0.1', 'port' => 11211, 'weight' => 99],
    ['host' => '127.0.0.1', 'port' => 11211, 'weight' => 99],
],

//redis服务器，具有索引为kv0和queue0的两个服务器
'redis'     => [
    'kv0'    => ['host' => '127.0.0.1', 'port' => '6379', 'pwd' => '', 'weight' => 99, 'timeout' => 1],
    'queue0' => ['host' => '127.0.0.1', 'port' => '6379', 'pwd' => '', 'weight' => 99, 'timeout' => 1],
],
~~~

* lin配置文件，参加不同组件的说明

### 注意
* 服务器的配置不建议动态改变。
* 服务器组件皆不提供多服务器的数据同步，请从服务器层面确保数据同步。
* 权重小于0的服务器将不会被使用。




