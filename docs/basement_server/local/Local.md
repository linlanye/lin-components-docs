Local类
----
namespace: `lin\basement\local`

包含如下类：

* **lin\basement\local\Local**

---

### 说明
操作本地文件读写

---

### 功能

* basement部分，[点击查看](https://github.com/linlanye/basement)
* 扩展功能：
    * 实例化时指定目录路径



#### 配置项

动态配置

分为两部分：

* `servers`相关部分，[点击查看](../README.md)。
* `lin`相关部分如下：

~~~php
<?php
'server'=>[
    'local' => [
        'path'  => 'data/path', //数据存放路径
        'debug' => true, //调试模式
    ],
]
~~~

#### 使用

~~~php
<?php

//实例化时指定目录路径
$Driver = new Local('/path');

//实例化时使用配置项路径
$Driver = new Local;
~~~


---


## API

#### 列表
~~~php
//非basement部分
public function __construct(string $path = null)
~~~