Formatter类
----
namespace: `lin\processor`

包含如下类：

* **lin\processor\Formatter**

---

### 说明
对数据进行格式化，输入一个数据值`raw`，将其格式化为`f(raw)`,f为任意格式化函数。通过继承本类后定义规则，然后调用规则进行数据批量格式化。提供`must`、`should`、`may`三种格式化模式，分别代表：无论字段存在与否都要格式化、字段存在便格式化、字段存在且非空才格式化。
除继承外，也可直接调用内置格式化方法对数据进行单个格式化。

---

### 功能

* 对数组中的字段格式化。
* 使用 `'.'` 访问数组深层字段。
* 提供常见[格式化方法](Function.md)。
* 提供`setting()`方法。




#### 配置项

动态配置

~~~php
<?php
'processor' => [
    'formatter' => [ //格式化器
        'default' => [
            'value' => function ($field) {return null;}, //使用回调对must模式下缺少的字段赋于默认值才进行格式化，入参为字段名
            'type'  => 'should', //默认格式化模式，可为must, should, may分别代表，必须格式化，存在才格式化，存在且非空格式化(trim后长度大于0)
        ],
    ],
]
~~~

#### 使用

使用`setRule(string $name, array $rules)`定义规则，其中`$name`为规则名，`$rules`为具体规则，格式形如

```
['字段: 模式' => '类里面的格式化方法或回调函数']

一般形式
['key1.key2, key3.key4: mode' => 'callable|method']

```
多个入参的格式化函数，在键名中使用 ',' 隔开；键值为格式化函数，优先级为：`回调函数` > `类自定义函数` > `类内置函数`

~~~php
<?php

//1.定义规则
class MyFormatter extends Formatter
{
    //在setting方法中定义不同的规则，以下用$raw表示原始数据
    protected function setting()
    {
        //定义一个格式化规则，使用类中格式化函数
        $this->setRule('my_rule', [
            //must模式，若原字段不存在，则调用配置项中的缺省值
            'id: must' => 'toInt', //强制将$raw['id']格式化为整数

            //should模式，字段存在才格式化
            'countdown: should' => 'toCountdown', //$raw['countdown']存在时，格式化为友好的倒计时格式

            //may模式，字段存在且非空才格式化
            'ip: may' => 'num2IP',//$raw['ip']存在且非空时，格式化为ip地址

            //格式化多个入参
            'info, symbol' => 'stripSymbol', //剔除$raw['info']中的$raw['symbol']字符
        ]);

        //定义一个格式化规则，使用回调或闭包
        $this->setRule('my_rule2', [
            'status' => function($value){
                return 1; //总是格式化为1
            },
        ]);

        //同理可定义多个不同规则
        ...
    }
}


//2.使用规则
$Formatter = new MyFormatter;

//保留所有的数据，包括未格式化的数据
$data = $Formatter->withRule('my_rule')->format($raw); //$data包含原规则中不存在的字段

//只保留格式化的数据
$data = $Formatter->withRule('my_rule')->format($raw, true); //$data只含有规则中定义的字段

//可同时使用多个规则，用 ',' 隔开
$Formatter->withRule('my_rule1, my_rule2')->format($raw);


//3.直接使用
$raw['id'] = $Formatter->toInt($raw['id']); //内置格式化函数见下方
~~~


---


## API

* 内置格式化函数，详细说明[点击查看](Function.md)

#### 列表
~~~php
final protected function setRule(string $name, array $rules): bool
final public function withRule(string $names): object
final public function format(array $raw, bool $onlyFormatted = false): array
~~~

#### 详细说明

**setRule()**: 只用在`setting()`中定义规则
```php
params:
    string $name  规则名
    array  $rules 规则数组，键名为原数据中的字段名和格式化模式，键值为格式化函数，多个格式化入参字段用 ',' 隔开，深层字段使用 '.' 访问。
return
    bool

$rules一般形式如：
[
    'key1.key2, key3.key4: mode' => 'callable|method', //优先级为：回调函数> 类自定义函数 > 类内置函数
]
```

**withRule()**: 指定当前格式化使用的规则，一次性使用。格式化完后，下一次格式化需重新指定。
```php
params:
    string $names 本次格式化使用的规则名，多个规则使用 ',' 隔开
return
    $this
```

**format()**: 格式化数据。
```php
params:
    array $raw                 待格式化的数据
    bool  $onlyFormatted=false 是否只保留格式化处理过的数据，否则包含原始未格式化的数据，默认为否
return
    array 格式化后的数据，未指定规则时返回原数据
```