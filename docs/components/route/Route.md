Route类
----
namespace: `lin\route`

包含如下类：

* **lin\route\Route**

---

### 说明
提供完整的路由规则创建、解析、执行，可作为框架的核心执行所在。

---

### 功能

* 创建任意风格的路由，如RESTful。
* 创建动态路由，使路由地址可解析为GET参数。
* 创建快速执行的闭包路由。
* 创建全局前置或后置执行。
* 创建未匹配时的通用执行。
* 执行指定的路由规则创建文件或自动加载所有路由规则创建文件
* 可临时关闭路由。
* 路由可全缓存化，包括闭包路由（依赖于php具体版本，闭包代码占用需超过两行）。
* 路由执行可通过终止符随时终止。




#### 配置项

动态配置

~~~php
<?php

'route'     => [
    'ci'        => false, //路由是否大小写不敏感
    'suffix'    => ['html', 'htm'], //支持的伪静态后缀名，数组索引为优先级顺序
    'path'      => $lin['route'], //存放路由规则的文件夹
    'terminal'  => [false], //终止符，返回该数组中任一符号则终止执行
    'namespace' => [ //执行类的命名空间前缀，在执行规则包含根'\'空间时不起作用，如'\Class'
        'pre'  => 'app\\layer\\middleware', //前置执行类的命名空间
        'main' => 'app\\layer', //主执行行类的命名空间
        'post' => 'app\\layer\\middleware', //后置执行类的命名空间
    ],
    'general'   => function ($url, $method) {
        $Class = 'app\\layer\\Error'; //未匹配到路由时使用的通用规则，入参为当前请求url和method。若非回调则不执行
        (new $Class)->status404();
    },
    'cache'     => [ //缓存相关
        'on'   => false, //缓存是否打开，缓存不会自动过期
        'path' => $lin['cache'] . '/route', //路由缓存路径
    ],
    'debug'     => true,
],
~~~

#### 使用

##### 创建规则
* 若未创建规则或未匹配到规则，路由会执行通用规则——来自配置项 `general` 的回调函数，若该项不为回调函数，则不执行。
* 规则为数组，形如`['匹配地址' => 执行规则 ]`，有以下三种格式：
    * '相对url' => '类名.方法名'
    * '相对url' => '闭包'
    * '相对url' => ['类名.方法名', '类名.方法名', '闭包'] ***（按数组元素依次执行）***
* 在`类名.方法名`的格式中，类名会自动加上来自配置项`namespace.main`的命名空间前缀，但若其首个字符为 '`\`'，则不使用命名空间前缀。
~~~php
<?php

//获得路由构建器
$Creator = Route::getCreator();

//构建仅匹配GET请求的路由
$Creator->create([
    '/' => 'Index.index', //匹配到请求地址为 '/' 时，执行app\layer\Index::index()
    '/closure' => function(){}, //匹配时执行闭包
    '/multi' => ['Index.index', 'Index.index2'], //匹配时依次执行两个类方法
], 'GET');

//构建动态路由，匹配到的参数会进入GET参数，若执行为闭包，则闭包的入参为匹配到的参数
$Creator->create([
    '/{id}-{page}' => function($id, $page){}, //匹配到形如/x-x格式的url时，将其解析为$_GET['id']和$_GET['page']参数，并执行闭包，闭包的入参为这两者
]);

//构建带前置和后置执行的路由
$Creator->withPre('Middleware.before')->withPost('Middleware.after')->create([
    '/middleware' => 'Index.index' //匹配时依次执行Middleware::before(), Index::index(), Middleware::after()
]);

//前置和后置执行可以有多个，用数组表示
$Creator->withPre(['Middleware.before', 'Middleware.before2']);

//前置和后置执行可以为闭包
$Creator->withPre(function(){});

//多次调用create()方法将合并所有规则，若规则冲突则抛出异常
~~~

##### 执行路由
* 执行时可通过`run()`方法加载配置项`path`下的所有规则定义文件，也可指定加载某些文件。使用 `*` 加载所有，使用 `,` 加载多个，`/` 结尾加载目录
* 匹配规则的优先级为：**静态** > **动态** > **通用**
* 执行时遭遇终止符则不再执行。假设执行流程为`Middleware::before(), Index::index(), Middleware::after()`，若此时`Middleware::before()`返回的是配置项`terminal`的元素（即终止符），则后续不再执行。

~~~php
<?php

//加载执行配置项'path'下所有的规则文件
Route::run();
Route::run('*');

//加载执行指定的规则文件
Route::run('my_route1, my_route2'); //加载两个规则文件
Route::run('admin/'); //加载admin目录下的所有规则文件

//不加载任何规则文件
Route::run(null);

//关闭路由并执行回调
Route::off(function(){});

//清除缓存
Route::clearCache(); //清除所有
Route::clearCache('my_route1, my_route2'); //指定清除my_route1, my_route2两个缓存

//重置，使路由可反复执行
Route::reset();

~~~
---


## API

#### 列表

~~~php
public static function run( ? string $files = '*') : bool
public static function off(callable $callable = null)
public static function getCreator(): object
public static function clearCache(string $files = '*'): bool
public static function reset(): bool

//路由规则构建器
public function withPre($pre): object
public function withPost($post): object
public function create(array $rules, string $methods = 'GET, POST'): bool
~~~

#### 详细说明

**::run()**: 加载路由规则，并执行路由
```php
params:
    string|null $files='*' 加载配置项path下的文件或目录，文件匹配规则参见glob()函数$pattern参数说明，加载多个使用 “,” 分割，null则不加载。
return
    bool 执行中断或未执行返回false，其余返回true
```

**::off()**: 关闭路由，并执行回调
```php
params:
    callable|null $callable=null 执行回调，null时不执行
return
    mixed|bool  回调的返回，或未执行回调时候返回true
```

**::getCreator()**: 获得路由构建器实例
```php
params:
    void
return
    object 路由构建器实例
```

**::clearCache()**: 清除指定缓存
```php
params:
    string $files='*' 待清除的原路由文件名，清除多个使用 “,” 分割，文件匹配规则参见glob()函数$pattern参数说明。
return
    bool
```

**::reset()**: 重置，使路由可反复执行
```php
params:
    void
return
    bool
```
----

##### 路由构建器

**withPre()**: 使当前`create()`定义的规则包含全局前置执行，一次性使用。
```php
params:
    string|Closure|array $pre 执行规则，string格式为'Class.method'。若为数组，则按数组元素执行，元素格式为string或Closure
return
    bool
```

**withPost()**: 使当前`create()`定义的规则包含全局后置执行，一次性使用。
```php
params:
    string|Closure|array $post 同上
return
    bool
```

**create()**: 创建路由执行规则
```php
params:
    array  $rules               规则数组，形如['url' => '执行规则']，执行规则格式见上诉
    string $methods='GET, POST' 与规则绑定的请求方法，若规则只绑定GET方法，则使用POST请求同一地址不会被匹配
return
    bool 规则为空时返回false，否则返回true
```