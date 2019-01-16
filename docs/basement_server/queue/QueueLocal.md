QueueLocal类
----
namespace: `lin\basement\queue`

包含如下类：

* **lin\basement\queue\QueueLocal**


---

### 说明

使用本地文件模拟的队列服务器，实现高速队列服务器的操作，适用于无队列服务器环境，
功能同Queue类([参见Queue说明](Queue.md))。

由于是模拟服务器，不具备[Queue](Queue.md)中罗列的方法。


---

### 功能

* basement部分，[点击查看](../../docs_basement/ServerQueue.md)
* 扩展功能：
    * 调节模拟服务器相关参数。


#### 配置项

[参见Queue](Queue.md)

#### 使用

以下方法为高级使用方法，用于自定义调节模拟服务器的性能，系统已内置较优方案，不建议使用
~~~php
<?php

//关闭服务器连接
$Driver = new QueueLocal;
$Driver->close(); //关闭所有队列连接
$Driver->close(true); //关闭当前队列连接

//冗余文件维护, 用于清除废弃数据。维护时候会阻塞读写
$Driver->maintain(); //清理当前队列废弃数据文件
$Driver->maintain(true); //清理所有队列废弃数据文件

//设置触发冗余文件维护的阈值
QueueLocal::setThreshold(1024*1024*100); //触发阈值为数据文件大小超过100Mb
~~~

---


## API

#### 列表
~~~php
//非basement部分
public function close(bool $current = false): bool
public function maintain(bool $isAll = false)
public function setThreshold(int $THRESHOLD): bool
~~~
