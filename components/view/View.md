View类
----
namespace: `lin\view`

包含如下类：

* **lin\view\View**

---

### 说明

提供简洁的视图模板处理，没有提供额外的模板语法，具有继承，包含，静态化页面等功能
可省略"{"、"}"、";"、"echo"等常见符号
如{:$a}等价于<php? echo $a; ?>
{if ($a==1)}{/if}等价于 <?php if ($a==1){} ?>

---

### 功能

*




#### 配置项

动态配置

~~~php
<?php
'view'      => [
    'charset'  => "utf8", //输出编码
    'path'     => $lin['view'], //模板文件路径
    'tag'      => [ //模板标签
        'left'         => '{', //左界定符，用于包裹模版语句
        'right'        => '}', //右界定符
        'begin_static' => 'STATIC', //静态化开始关键字，将其中内容输出为静态内容，而不是php代码
        'end_static'   => '/STATIC', //静态化结束关键字
        'escape'       => '\\', //转义标签，
        'location'     => 'LOCATION', //继承位置所在关键字
        'extends'      => 'EXTENDS', //界定符内继承视图关键字, 如{extends parent_view}
        'include'      => 'INCLUDE', //引入视图文件关键字，如{include some_view}
    ],
    'cache'    => [ //缓存相关
        'life' => -1, //有效期，单位s，0则不过期，小于0立即过期
        'path' => $lin['cache'] . '/view', //缓存路径
    ],
    'security' => 'htmlspecialchars', //对分配变量中的每一个标量做安全处理的回调，入参为每一个标量值
    'debug'    => true,
],
~~~

#### 使用

~~~php
<?php


~~~


---


## API

#### 列表
~~~php
public function withData(array $data): object
public function assign(string $name, $value): object
public function show(string $view): void
public function getContents(string $view): string
public function getData(): array

~~~

#### 详细说明

**withData()**: 批量分配视图模板的变量，多次调用则合并数据。
```php
params:
    array $data 以键名为模板变量名，键值为变量值
return
	$this
```

**assign()**: 分配一个模板变量。
```php
params:
	string $name  分配的视图模板变量名
	mixed  $value 变量值
return
	$this
```

**show()**: 解析目标视图并显示，同时清空分配的视图模板变量。
```php
params:
	string $view 视图名
return
	void
```

**getContents()**: 解析目标视图并获得解析后的内容，同时清空分配的视图模板变量。
```php
params:
	string $view 视图名
return
	string 视图解析后的内容
```

**getData()**: 获得已分配的模板变量。
```php
params:
	void
return
	array 已分配的模板变量
```