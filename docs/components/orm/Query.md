Query类
----
namespace: `lin\orm\query`

包含如下类：

* **lin\orm\query\Query**

---

### 说明
本类用于构建数据库的快速查询，除包含[SQL语句构建](SQLCreator.md)的相关方法外，还包括自动事务、生成器、安全操作等一系列扩展功能。

---

### 功能

* 构建任意用于增删改查的SQL语句并执行。（[点击查看](SQLCreator.md)）
* 自动事务，无需手动提交回滚。
* 安全化写操作，对更新和删除强制要求携带条件语句才可操作。
* 对多条查找记录，可获得一个生成器用于迭代读取，从而节省内存。
* 可自定义操作驱动。
* 获得自增ID。


#### 注意
* 聚合查询（如count，sum等）结果只返回**标量值**（成功）和`null`（失败）。
* 读（select）操作返回**数组**（成功）或`null`（失败）
* 写（update、insert、delete）操作只返回**影响的记录数**。对`insert`操作，如需获得自增ID，可调用`lastID()`方法

#### 配置项

无


#### 使用
~~~php
<?php
$Query = new Query;

//获得操作结果
$Query->select(); //多记录查询
$Query->one(); //单记录查询
$Query->count(); //聚合查询，包括sum、max、min、avg等
$Query->where('id', 1)->update(); //更新
$Query->where('id', 1)->delete(); //删除
$Query->insert(); //插入
$Query->replace(); //替代，有些数据库可能不支持！

... //更多方法见SQLCreator说明

//获得最近自增ID
$Query->lastID(); //返回参考Basement的ServerSQL::lastID()

//使用自动事务，事务中所有写操都成功才提交，否则回滚
$Query->autoTrans(function($Query){
    $Query->insert(['data' => 2]); //插入新记录
    $Query->where('id', 1)->update(['data' => 1]); //更新id为1的data字段
})

//获得生成器，用于多记录查询结果时迭代
$Query->yield()->select();

//自定义驱动
$Query->setDriver($Driver); //设置当前实例的驱动
$Query->getDriver($Driver); //获得当前实例的驱动
~~~


---


#### API

#### 列表
~~~php

//内置方法
public function yield(): object
public function lastID():  ? string
public function autoTrans(Closure $Closure) : bool
public function setDriver(object $Driver): object
public function getDriver(): object
~~~

#### 详细说明

**yield()**: 用于select()方法时，返回一个生成器，用于迭代单条记录，从而节约内存
```php
params:
    void
return
    object 一个生成器，该生成器的返回值是影响的记录数
```

**lastID()**: 获得最近自增ID
```php
params:
    void
return
    string|null 失败返回null
```

**autoTrans()**: 开始自动事务，所有写操作全部成功才提交，否则回滚。
```php
params:
    Closure 用于编写多个操作逻辑，闭包的入参为当前Query实例，在闭包中书写的任意写操作必须全部成功才提交，否则回滚
return
    bool 事务成功与否
```

**setDriver()**: 设置当前实例的驱动
```php
params:
    object 一个满足Basement的ServerSQL标准的对象
return
    $this
```

**getDriver()**: 获得当前实例的驱动
```php
params:
    void
return
    object 当前使用的驱动，为一个满足Basement的ServerSQL标准的对象
```

#### SQL语句构建

参考[SQLCreator说明](SQLCreator.md)