Session类
----
namespace: `lin\session`

包含如下类：

* **lin\session\Session**

---

### 说明

提供自定义session处理

---

### 功能

* 使用本地文件管理session
* 使用KV服务器管理session
* 使用SQL服务器管理session
* 使用自定义的类管理session



#### 配置项

静态配置

~~~php
<?php
'session'   => [
    'life'   => 1200, //过期时长，单位秒，0则永不过期
    'use'    => 'local', //当前使用的服务器
    'server' => [ //可选的服务器列表
        'local'  => [ //使用本地存储
            'path' =>   'path/to/session', //存放路径
            'deep' => 0, //子目录深度
        ],
        'kv'     => [ //使用kv服务器
            'prefix' => '_ssid_', //使用的key前缀
        ],
        'sql'    => [ //使用sql服务器
            'table'  => 'sessions', //表名
            'fields' => [ //键值为数据库字段名,
                'id'      => 'session_id', //字段类型根据session_id长度来定，主键，最少char(32)
                'content' => 'content', //可空text类型，数据增量无上限，sessoin内容越多越大
                'time'    => 'created_time', //创建日期，最少3字节int
            ],
        ],
        'custom' => 'SessionHandlerClass', //使用自定义的处理类，需实现SessionHandlerInterface
    ],
],
~~~

#### 使用

~~~php
<?php

//一行代码即可，其后依旧使用$_SESSION变量读写
Session::run();
~~~