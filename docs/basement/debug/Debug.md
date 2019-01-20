Debug类
----
namespace: `lin\basement\debug`

包含如下类：

* **lin\basement\debug\Debug**

---

### 功能

* basement部分，[点击查看](../../docs_basement/Debug.md)
* 扩展功能：
    * 实例化时指定调试标识名。
    * 使用两个相同`flag('flag_name')`代替`beginFlag('flag_name')和endFlag('flag_name')`。
    * 清空数据。



#### 配置项

动态配置

~~~php
<?php

'debug'     => [
    'panel'   => [ //调试面板
        'display' => 'only', //显示模式，空或none不显示，both显示全部,only显示小图标
        'name'    => [
            'prior'  => 'SYSTEM', //优先选中标识名
            'hidden' => [], //隐藏的标识名
        ],
    ],
    'lang'    => 'none', //多语言显示，空或none为关闭，only为多语言仅用于显示，但数据依然为原文，both为数据和显示都为多语言
    'default' => [
        'name' => 'LIN', //默认调试标识名
    ],
],
~~~

#### 使用

~~~php
<?php

//实例化并指定标识名
new Debug('lin');

//运行调试
Debug::run();

//清除标识调试数据
Debug::clean('lin'); //清空标识为lin的调试数据
Debug::clean(); //清空所有调试数据

//清除flag里的系统数据
Debug::cleanFlag('lin'); //清空flag为lin的系统数据
Debug::cleanFlag(); //清空所有flag数据
~~~


---


### API

#### 列表
~~~php
//非basement部分
public function __construct(string $name = '')
public static function run(): bool
public static function flag(string $flagName): bool
public static function clean(string $name = ''): bool
public static function cleanFlag(string $flagName = ''): bool
~~~

#### 详细说明
**__construct()**: 构造函数
```php
params:
    string $name='' 标识名，等价于使用setName()方法，传入空则取配置项中默认值
```

**::run()**: 启动调试
```php
params:
    void
return:
    bool 是否成功
```

**::flag()**: 连续调用两次等效于`beginFlag()和endFlag()`
```php
params:
    string $name='' 标识名
return:
    bool 是否成功
```

**::clean()**: 清除目标调试数据
```php
params:
    string $name='' 标识名，空则清除所有
return:
    bool 是否成功
```

**::cleanFlag()**: 清除目标flag数据
```php
params:
    string $flagName='' flag名，空则清除所有
return:
    bool 是否成功
```

