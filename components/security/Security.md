Security类
----
namespace: `lin\security`

包含如下类：

* **lin\security\Security**

---

### 说明


---

### 功能

*




#### 配置项

动态配置

~~~php
<?php
'security'  => [
    'use'     => 'local', //使用的服务器来管理安全数据
    'server'  => [
        'local' => [ //使用本地存储
            'path' => 'path/to/security', //存放路径
        ],
        'sql'   => [ //使用sql服务器
            'table'  => 'security', //表名
            'fields' => [ //键值为数据库字段名,
                'id'      => 'client_id', //最少char(70),主键
                'type'    => 'type', //客户端类型，临时或正式，最少1字节int
                'content' => 'content', //可空text类型，数据增量无上限，有效场景越多越大，
                'time'    => 'created_time', //客户端创建时间，最少4字节int
            ],
        ],
    ],
    'gc'      => [ //对临时客户端执行垃圾回收
        'probability' => 0.0001, //触发概率
        'max_life'    => 1800, //临时客户端的静止生命期(s)，同session机制
    ],
    'default' => [
        'cost' => 4, //加密token的消耗等级，越高越安全，见password_hash函数
        'life' => 1200, //默认场景时效，单位s
    ],
    'image'   => [ //图片专用(即图片验证码)
        'resolution' => [130, 50], //生成的图片分辨率，宽和高
        'length'     => 5, //验证码长度，最短1位，最长20位
        'level'      => 2, //复杂等级，最高级6，最低级0，等级越高越耗资源
        'seed'       => '23456789ABCDEFGHJKLMNPQRSTUWXYZ', //生成验证码的种子
        'background' => [], //背景图片(完整路径名)，若有则随机选择
        'ttf'        => [ //随机使用的ttf字体库，
            'lin-components/src/security/structure/captcha/stencil-four.ttf',
        ],
    ],
    'debug'   => true,
],

~~~

#### 使用

~~~php
<?php


~~~


---


## API

#### 列表
~~~php


~~~

#### 详细说明

**()**:
```php
params:
    string $param 参数名
return
	mixed|null 失败返回null
```