Layer类
----
namespace: `lin\layer`

包含如下类：

* **lin\layer\Layer**

---

### 说明
本类提供了对**LBA架构**的实现，并整合了对Lin的各个组件的快速访问，自身并不提供任何独有的功能。
此外还提供了**流水化流程**功能，让层与层之间的交互简单明了，省略大量代码。

关于LBA架构介绍如下：

> LBA（Layer, Block, Affix）架构由层、块、摆件三个部分构成，由**林澜叶**独自提出。
>
* **层**：核心架构所在，由整套不同的逻辑单元组成，彼此之间相互独立，是整个应用的骨架部分。如缓存层、数据访问层、控制器层、响应层等等。不同的层提供不同的应用场景，一个层可以看作一个用于调度不同功能的类。
* **块**：依托于层而存在，为层提供一种功能，是对层功能的强化，是整个应用的血肉部分。一个块可以看作是具有某个功能类，不同的块在同一个层中构成一个完备的结构。如在数据访问层中，数据模型提供对数据库的对象化操作，数据格式化器和映射器则提供存储数据到应用数据的一个映射。
* **摆件**：作为对层的点缀或装饰，是一种可选的功能，它的添加和移除对整个应用架构没有影响。不同的摆件可以看作是一种功能扩展，它可以是一个类，也可以是一个脚本，一句代码，起到强化应用的作用。如视图模版、路由文件、语言包在小型应用中都可以看作摆件，可以无需视图（API开发），也可以无需路由(仅通过层来调度)，更可以无需语言包。
>
MVC的Model对应块，View对应LBA的摆件，Controller则对应层。基于MVC的各种变体也能在LBA架构中找到对应，实际上LBA正是对这套架构体系的一个更抽象的扩展，它能够适用于更大型的应用架构中。值得注意的是，LBA架构并没有对功能进行约束，开发者需根据实际情况选型。例如小型应用中路由可以看作摆件，但在大型应用中，路由则应作为层。

关于流水化流程：

> 这里指的是一个完整的程序执行，产品（数据）在流水线（执行逻辑）上往前流动，当在某个环节被查出不合格时就被踢出（终止）。
>
在LBA架构中，每一个环节对应一个层的一个方法，数据在预先定义好的各个环节上依次执行，当数据不满足某个环节的要求时，后续执行被终止。使用这种方式可以节省大量`if-else`语句，并且结构简单，耦合度低。

> [具体参见](Flow.md)

---

### 功能

* 提供层与层之间的快速访问。
* 提供块的快速调用。
* 整合了Lin的不同功能性组件的实例，实现快速调用。
* 流水化流程。


#### 注意

本类提供`setting()`方法。


#### 配置项

动态配置

~~~php
<?php

'layer'     => [
    'namespace' => [
        'layer' => 'app\\layer', //层所处命名空间
        'block' => 'app\\block', //块所处命名空间
    ],
    'debug'     => true, //调试模式
],
~~~

#### 使用

~~~php
<?php

//需继承后通过编写类及其方法使用
class Controller extends Layer
{
	//设置使用封装的功能组件
	protected function setting()
	{
		$this->use('http, log, local, queue, kv, sql');
		//将获得如下对象：
		//http
        $this->Request;
        $this->Response;
        //log
        $this->Log;
        //local
        $this->Local;
 		//queue
        $this->Queue;
 		//kv
        $this->KV;
        //sql
        $this->SQL;
        $this->Query;
	}

	//使用层、块访问
	public function index()
	{
		//获得层实例
		self::layer('Controller2'); // 返回app\layer\Controller2实例

		//获得块实例
		self::block('Block2'); // 返回app\block\Block2实例
		self::block('Block2', $arg1, $arg2); //以arg1和arg2作为构造参数实例化
		//获得块类名
		self::blockName('Block2'); //返回app\block\Block2

		//流程流水化，依次调用Controller2的index方法和index2方法，入参皆为流实例
		$Flow = self::flow([
			'Controller2.index',
			'Controller2.index2'
		], $data);
	}
}
~~~


---


## API

#### 列表
~~~php
protected function setting()
final protected function use(string $utils)
final protected static function layer(string $layer): object
final protected static function block(string $block, ...$args): object
final protected static function blockName(string $block): string
final protected static function flow(array $layers, $data = null): object
~~~

#### 详细说明

**use()**: 获得Lin部分组件的实例对象
```php
params:
    string $utils 欲使用的功能，多个用 ',' 隔开，包含http, log, local, queue, kv, sql六类
return
	mixed|null 失败返回null
```

**::layer()**: 获得层实例
```php
params:
    string $layer 目标层类名，命名空间前缀为配置项设置，满足psr-4方式调用更深目录的类
return
	object 目标层实例
```

**::block()**: 获得块实例
```php
params:
    string $block   目标块类名，命名空间前缀为配置项设置，满足psr-4方式调用更深目录的类
    array  $args=[] 实例化时对构造参数，见php不定参
return
	object 目标块实例
```

**::blockName()**: 获得块完整类名
```php
params:
    string $block 目标块类名，命名空间前缀为配置项设置，满足psr-4方式调用更深目录的类
return
	string 目标块的完整类名
```

**::flow()**: 开始流水化流程
```php
params:
    array $layers    整个执行流程，数组元素为依次执行的层方法，每一个层方法的入参都为Flow对象，满足psr-4方式调用更深目录的类，使用 '.'隔开类名（在前）与方法名（在后）
    mixed $data=null 流携带的数据，默认无
return
	Flow 流对象，参加Flow说明
```