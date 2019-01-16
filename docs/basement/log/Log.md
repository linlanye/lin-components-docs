Log类
----
namespace: `lin\basement\log`

包含如下类：

* **lin\basement\log\Log**

---

### 功能

* basement部分，[点击查看](../../docs_basement/Log.md)
* 扩展功能：
    * 在脚本生命期结束后一次性写入。
    * 可使用队列、关系型数据库、本地文件存储日志。



#### 配置项

动态配置

~~~php
<?php

'log'       => [
    'use'     => 'local', //使用的服务器
    'server'  => [
        'local' => [//用本地文件存储
            'path'      => 'path/to/log',
            'size'      => 100 * 1024 * 1024, //单个日志文件大小最大值，最大不超过2gb
            'frequency' => 720, //使用文件夹对日志汇总的频率,0不汇总，(0,24]按小时,(24,720]按天，>720按月
            'format'    => function ($type, $content, $time) {
                return "[$type]; " . date('Y-m-d H:i:s', $time) . "; $content"; //对日志进行格式化后写入
            },
        ],
        'sql'   => [//用关系型数据库存储
            'table'  => 'logs',//表名
            'fields' => [ //表字段
                'name'    => 'name', //日志名，建议varchar类型
                'type'    => 'type', //日志类型，建议varchar类型
                'content' => 'content', //内容，text类型，容量根据单条日志内容上限定
                'time'    => 'created_time', //创建时间,最少4字节int
            ],
        ],
        'queue' => [ //使用队列进行异步操作,入参为日志类型，日志内容，日志创建时间
            'format' => function ($type, $content, $time) {
                return ['type' => $type, 'content' => $content, 'time' => $time]; //对日志进行格式化后存入队列
            },
            'prefix' => '_linlog_', //队列名前缀
        ],
    ],
    'default' => [
        'name' => 'linlog', //默认日志名
    ],
    'debug'   => true,//调试模式
],

~~~

#### 其他

参见basement[点击查看](../../docs_basement/Log.md)。


