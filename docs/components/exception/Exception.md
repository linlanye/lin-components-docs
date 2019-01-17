Exception类
----
namespace: `lin\exception\Exception`

包含如下类：

* **lin\exception\Exception**

---

### 功能

* 自定义错误异常处理。


#### 配置项

静态配置

~~~php
<?php

'exception' => [
    //异常处理
    'exception' => [
        'lang'     => 'both', //多语言显示，空或none为关闭，only为仅用于显示，但日志记录仍为原文，both为都用于显示和日志记录
        'default'  => [
            'type' => 'General', //默认的异常类型
        ],
        'log'      => [
            'on'   => true, //是否日志记录异常
            'name' => 'exception', //日志名
        ],
        'show'     => true, //是否显示异常
        'callback' => '', //异常发生时回调函数，空则不回调，入参为异常类$Exception
    ],

    //错误处理
    'error'     => [
        'lang'     => 'none',
        'log'      => [
            'on'   => true, //是否日志记录错误
            'name' => 'error', //日志名
        ],
        'show'     => true, //是否显示错误
        'callback' => '', //错误发生时回调函数，空则不回调，入参为$errNo, $errStr, $errFile, $errLine
    ],
],
~~~

#### 使用

~~~php
<?php
//开启
Exception::run();

//重置
Exception::reset();
~~~


---


## API

#### 列表
~~~php
public static function run()
public static function reset(): bool
~~~
