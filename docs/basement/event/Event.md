Event类
----
namespace: `lin\basement\event`

包含如下类：

* **lin\basement\event\Event**

---

### 功能

* basement部分，[点击查看](../../docs_basement/Event.md)
* 扩展功能：
    * 绑定一次性事件。
    * 自动读取指定事件文件。
    * 清空数据。


#### 配置项

静态配置

~~~php
<?php

'event'     => [
    'path'  => 'events/file/path', //存放定义事件的文件夹
    'debug' => true, //调试模式
],
~~~

#### 使用

~~~php
<?php

//自动加载配置项path中的所有文件
Event::run();
Event::run('*');

//加载配置项path中的指定文件或指定目录
Event::run('file, folder/*'); //同时加载path下的file文件和folder目录里的所有文件

//不加载任何文件
Event::run(null);

//绑定一次性事件
Event::one('lin','callback'); //绑定一次性事件lin

//清除事件
Event::clean('lin'); //清空lin事件
Event::clean(); //清空所有事件

//重置
Event::reset(); //清除数据并可重新加载事件文件
~~~


---


## API

#### 列表
~~~php
//非basement部分
public static function run(? string $files = '*'): bool
public static function one(string $event, callable $callable): bool
public static function clean(string $event = ''): bool
public static function reset(): bool
~~~

#### 详细说明

**::run()**: 自动加载指定事件文件
```php
params:
    ? string $files='*' 加载配置项path下的文件或目录，文件匹配规则参见glob()函数$pattern参数说明，加载多个使用 “,” 分割，null则不加载。
return:
    bool 是否成功
```

**::one()**: 绑定一次性事件
```php
params:
    string   $event    事件名
    callable $callable 执行回调
return:
    bool 是否成功
```

**::clean()**: 清除目标事件
```php
params:
    string $event='' 事件名，空则清除所有
return:
    bool 是否成功
```

**::reset()**: 重置
```php
params:
    void
return:
    bool 是否成功
```