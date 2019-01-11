Lang类
----
namespace: `lin\basement\Lang`

包含如下类：

* **lin\basement\lang\Lang**

---

### 功能

* basement部分，[点击查看](../../docs_basement/Lang.md)
* 扩展功能：
    * 实例化时可指定标识名。
    * 清空映射数据。
    * 重置，清空所有数据和自动加载规则。



#### 配置项

动态配置

~~~php
<?php

'lang'      => [
    'default' => [ //默认映射和自动加载
        'name'     => 'lin', //默认标识名
        'map'      => '', //找不到映射时使用的回调，入参为源字符，非回调则返回原值
        'i18n'     => 'en', //默认语言类型
        'autoload' => function ($label, $i18n){
            $lang =  "path/to/lang/$label.$i18n.php"; //默认自动映射规则为文件加载
            if (file_exists($lang)) {
                return include $lang; //返回数组
            }
            return [];
        },
    ],
],
~~~

#### 使用

~~~php
<?php

//实例化并指定标识名
new Lang('lin');

//运行调试
Lang::run();

//清除指定i18n中的所有语言数据
Lang::clean('en'); //清空en语言下的所有数据
Lang::clean(); //清空所有语言数据

//清空所有数据和自动加载规则。
Lang::reset();
~~~


---


## API

#### 列表
~~~php
//非basement部分
public static function __construct($name = '')
public static function clean(string $i18n = ''): bool
public static function reset(): bool
~~~

#### 详细说明
**__construct()**: 构造函数
```php
params:
    string $name='' 标识名，等价于使用setName()方法，传入空则取配置项中默认值
```

**::clean()**: 清除目标语言数据
```php
params:
    string $i18n='' 语言名，空则清空所有
return:
    bool 是否成功
```

**::reset()**: 清空所有数据和自动加载规则
```php
params:
    void
return:
    bool 是否成功
```
