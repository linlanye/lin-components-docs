View类
----
namespace: `lin\view`

包含如下类：

* **lin\view\View**

---

### 说明

提供简洁的视图模板处理，没有提供额外的模板语法，只简化输出和函数调用，和原生`php`语法保持一致。具有页面继承，页面引入，页面局部静态化等功能。

---

### 功能

* 简化嵌入html页面的php代码。
* 可在页面任意位置进行单继承、引入，静态化。
* 自动安全化模板变量，防止XSS。
* 页面缓存控制。



#### 配置项

动态配置

~~~php
<?php
'view'      => [
    'charset'  => "utf8", //输出编码
    'path'     => 'path/to/view', //模板文件路径
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
        'path' => 'path/to/cache/view', //缓存路径
    ],
    'security' => 'htmlspecialchars', //对分配变量中的每一个标量做安全处理的回调，入参为每一个标量值
    'debug'    => true,
],
~~~

#### 使用

* 编写`.php`文件，文件中主要编写`html`代码和模板语句。
* 模板语句以配置项`tag.left`和`tag.right`这两个左右界定符包裹。
* 以冒号开头的语句皆为输出，如`{:$var} 等价于 echo $var;`；`{:md5($)} 等价于 echo md5($var);`。
* 输出变量时可以省略 `$` 符号，如`{:var}`。
* `switch`语句中的`case:`和`default:`可省略 `:`，如`{case 'lin'}等价于 {case 'lin':}`
* 可省略`php`语句末尾的 `;`。
* 所有视图模板中的变量都来自`withData()`或`assign()`方法中的数据。
* 若页面中出现模板标签的关键字，可使用配置项`tag.escape`中定义的转移标签对其转义，使其不被解析。

##### 基本语法
~~~html
<html>
	<!--输出模板变量-->
	{:var}
	{:$var}

	<!--输出函数值-->
	{:md5($var)}

	<!--if语句 -->
	{if ($var ==' lin')}
		<p>lin框架</p>
	{elseif}
		<p>简单</p>
	{else}
		<p>高效</p>
	{/if}

	<!--while语句 -->
	{while ($var ==' lin')}
		<p>lin框架</p>
	{/while}

	<!--while语句 -->
	{while ($var ==' lin')}
		<p>lin框架</p>
	{/while}

	<!--foreach语句-->
	{foreach ($array as $k=>$v)}
		<p>{:v}</p>
	{/foreach}

	<!--switch语句-->
	{switch ($var)}
		{case 'lin'}
			<p>lin框架</p>
			{break}
		{default}
			<p>高效</p>
	{/switch}


	<!--其他php语法和原生一致，只需在左右界定符中编写即可-->
</html>
~~~

##### 继承

使子页面嵌入到父页面的指定位置中形成一个新的页面，嵌入的父页面位置称为继承点。

* 仅支持单继承，即子页面只能嵌入到一个父页面中。
* 父页面中的继承点可以任意指定，若无指定，则默认嵌入到父页面末尾。
* 父页面中的继承点可以有任意多个，在多继承点情况下，会重复使得子页面嵌入到父页面中多个位置。
* 页面继承可以进行任意次，如`祖父页面-父页面-子页面-孙子页面`。
* 子页面中的继承关键字可以位于其任意位置，其继承结果不变。
* 子页面中的继承关键字只以第一个继承标识为准，若继承的页面位于多级目录下，使用 '/' 进行分割，如`{EXTENDS layouts/common}`。

假设parent.php文件内容如下
~~~html
<html>
	<p>我是父页面</p>
	<p>{LOCATION}</p> <!--继承点-->
</html>
~~~

假设child.php文件内容如下
~~~html
<html>
	<p>我是子页面</p>
	{EXTENDS parent} <!--继承标识，可放在任意位置-->
</html>
~~~

则解析chilid.php文件最终生成样式如下
~~~html
<html>
	<p>我是父页面</p>
	<p>我是子页面</p>
</html>
~~~

##### 引入

将某个页面的内容全部拷入当期页面的指定位置。

* 引入类似于`include`。
* 可在一个页面引入任意多个指定页面。
* 一个页面同时出现继承和引入时，先解析继承后解析引入。

假设my_view.php文件内容如下
~~~html
<html>
	<p>lin框架</p>
	{INCLUDE child}
</html>
~~~

则解析后生成样式如下
~~~html
<html>
	<p>lin框架</p>
	<p>我是父页面</p>
	<p>我是子页面</p>
</html>
~~~

##### 静态化

解析后的页面，生成的是`php`语法，即使页面缓存后，每次也要执行`php`语句。静态化就是让页面进一步解析为`php`语句的输出结果。对不常更新的数据，使用静态化可以达到加速执行的效果。

* 静态区域以配置项`tag.begin_static`和`tag.end_static`这两个静态化开始标签和结束标签包裹。
* 静态化开始标签和结束标签必须成对出现。

假设static.php文件内容如下
~~~html
<html>
{STATIC}
	{foreach ($array as $v)}
		<p>{:v}</p> <!--$array为['lin框架', '简单', '高效']-->
	{/foreach}
{/STATIC}
</html>
~~~

则解析后生成样式如下
~~~html
<html>
	<p>lin框架</p>
	<p>简单</p>
	<p>高效</p>
</html>
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
public static function clearCache(string $files = '*'): bool
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

**::clearCache()**: 清除指定缓存
```php
params:
	string $files='*' 待清除的原视图文件名，清除多个使用 “,” 分割，文件匹配规则参见glob()函数$pattern参数说明。
return
	bool
```