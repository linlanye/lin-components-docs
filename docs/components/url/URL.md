URL类
----
namespace: `lin\url\URL`

包含如下类：

* **lin\url\URL**

---

### 说明
在原始输入url和目标url建立一层映射，可自动管理url的生成。

---

### 功能

* 生成普通url。
* 生成资源文件专用的url。
* 通过配置项可生成任意风格的url。



#### 配置项

静态配置

~~~php
<?php
'url'       => [ //构建url
    'dynamic' => [ //使用回调生成完整path，入参为输入的当前有效域，输入的路径值，当前入口脚本名
        'path'  => function ($domin, $path, $script) {
            if ($path == '/') {
                return "http://$domin";
            }
            $path = trim($path, '/'); //伪静态url规则
            return "http://$domin/$path.html";
        },
        'query' => function ($params) {
            return '?' . http_build_query($params); //使用回调生产查询(GET)参数
        },
    ],
    'static'  => [ //构建静态资源专用的path，位于public目录下
        'path' => function ($domin, $path, $script) {
            $path = trim($path, '/');
            return "http://$domin/resource/$path";
        },
    ],
],
~~~

#### 使用

~~~php
<?php

//生成一般url地址，通过配置项 'dynamic.path' 回调返回处理后的完整url
$url = URL::get('lin'); //不带get参数，http://www.lin-php.com/lin.html
$url = URL::get('lin', ['id' => 1]); //带get参数，http://www.lin-php.com/lin.html?id=1

//生成资源专用的url地址，通过配置项 'static.path' 回调返回处理后的完整url
$js = URL::getStatic('lin.js'); //http://www.lin-php.com/resource/lin.js
$img = URL::getStatic('imgs/lin.png'); //http://www.lin-php.com/resource/imgs/lin.png

//获得当前域名和入口脚本名
$params = URL::getParameters(); //['domin' => 'www.lin.com', 'script' => 'index.php']
~~~


---


## API

#### 列表
~~~php
public static function get(string $path, array $params = null): string
public static function getStatic(string $path): string
public static function getParameters(): array
public static function reset(): bool
~~~

#### 详细说明

**::get()**: 生成一般url地址，可带参数。url生成风格来自配置项`dynamic.path`的返回值，当该配置项为非回调时则返回原值
```php
params:
    string $path        原始url
	array  $params=null 需要并入url中的参数
return
	string
```

**::getStatic()**: 生成资源专用的静态url地址，url生成风格来自配置项`static.path`的返回值，当该配置项为非回调时则返回原值
```php
params:
    string $path 原始url
return
	string
```

**::getParameters()**: 获得有效域名和入口脚本名参数
```php
params:
    void
return
	array 格式形如['domin' => '当前站点域名', 'script' => '当前入口脚本名']
```

**::reset()**: 重置，使得配置项可以重新读入
```php
params:
	void
return
	bool
```