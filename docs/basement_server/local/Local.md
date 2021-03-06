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

* basement部分，[点击查看](../../docs_basement/ServerLocal.md)
* 扩展功能：
    * 实例化时指定目录路径



#### 配置项

动态配置

分为两部分：

* `servers`相关部分，[点击查看](../README.md)。
* 组件部分如下：

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

//删除文件为非空目录时，不予删除
$Driver->remove('not_empty_dir'); //不会删除

//删除时候指定空字符串，则会删除path目录。
$Driver->setPath('not_empty_dir');
$Driver->remove(''); //目录not_empty_dir被删除
~~~


---


### API

#### 列表
~~~php
//非basement部分
public function __construct(string $path = null)
~~~