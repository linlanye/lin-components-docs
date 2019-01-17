Layer类
----
namespace: `lin\layer`

包含如下类：

* **lin\layer\Layer**

---

### 说明
本类提供了对**[LBA架构](../../framework/LBA.md)**的实现，并整合了对Lin的各个组件的快速访问，自身并不提供任何独有的功能，也是**Lin**中唯一和其他组件耦合的组件。

除**[LBA架构](../../framework/LBA.md)**外，还提供了**流水化流程**功能，让层与层之间的交互简单明了，省略大量代码。


关于流水化流程：

> 指的是一个完整的程序执行，产品（数据）在流水线（执行逻辑）上自动往前流动，当在某个环节被查出不合格时就被踢出（终止）。
>
在LBA架构中，每一个环节对应一个层的一个方法，数据在预先定义好的各个环节上依次执行，当数据不满足某个环节的要求时，后续执行被终止。使用这种方式可以节省大量`if-else`语句，并且结构简单，耦合度低。

> 具体参见[Flow类说明](Flow.md)

---

### 功能

* 提供层与层之间的快速访问。
* 提供块的快速调用。
* 整合了Lin的部分组件实例，实现对常用组件的快速调用。
* 流水化流程功能。


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
        $this->Request; //basement Request类
        $this->Response; //响应类

        //log
        $this->Log; //basement ServerLog类

        //local
        $this->Local; //basement ServerLocal类

        //queue
        $this->Queue; //basement ServerQueue类

        //kv
        $this->KV; //basement ServerKV类

        //sql
        $this->SQL; //basement ServerSQL类
        $this->Query; //查询类
    }

    //使用层、块访问
    public function index()
    {
        //获得层实例
        self::layer('Controller2'); // 返回app\layer\Controller2实例

        //获得块实例
        self::block('Block2'); // 返回app\block\Block2实例
        self::block('Block2', $arg1, $arg2); //以$arg1和$arg2作为构造参数实例化app\block\Block2

        //获得块类名
        self::blockName('Block2'); //返回字符串'app\block\Block2'

        //流程流水化，依次调用Controller2的index方法和index2方法，这二者方法的入参皆为Flow实例
        $Flow = self::flow([
            'Controller2.index',
            'Controller2.index2'
        ], $data); //Flow->data值为$data
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
    string $utils 欲使用的组件，多个用 ',' 隔开，包含http, log, local, queue, kv, sql六类。使用所有用 '*' 表示。
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
    array $layers    整个执行流程，数组元素为依次执行的层方法，每一个层方法的入参都为Flow对象，满足psr-4方式调用更深目录的类，格式形如 'class(类名).method(方法名)'
    mixed $data=null Flow实例携带的数据，也即$Flow->data值，默认无
return
    Flow 流对象，参加Flow说明
```