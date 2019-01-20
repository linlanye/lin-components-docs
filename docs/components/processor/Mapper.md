Mapper类
----
namespace: `lin\processor`

包含如下类：

* **lin\processor\Mapper**

---

### 说明
将数组中的任意字段映射为指定字段，值保持不变。支持使用 `'.'` 访问多维数组元素。
提供`must`、`should`、`may`三种映射模式，分别代表：无论字段存在与否都要映射、字段存在便映射、字段存在且非空才映射（trim后长度大于0）。
使用时需继承本类，并定义映射规则。
---

### 功能

* 对数组中的字段映射。
* 使用 `'.'` 映射深层字段。
* 提供`setting()`方法。



#### 配置项

动态配置

~~~php
<?php
'processor' => [
    'mapper'    => [ //映射器
        'default' => [
            'value' => function ($field) {return null;}, //使用回调对must模式下缺少的字段赋值，入参为字段名
            'type'  => 'should', //未指定映射模式时，默认的模式，可为must, should, may分别代表，必须格式化，存在才格式化，存在且非空格式化(trim后长度大于0)
        ],
    ],
]
~~~

#### 使用

使用`setRule(string $name, array $rules)`定义规则，其中`$name`为规则名，`$rules`为具体规则，格式形如
```
['原字段: 模式' => '目标字段']

一般形式
['key1.key2: mode' => 'a1.a2, b1.b2.bn'] //将任意字段映射为任意多个目标字段
```

##### 定义规则
~~~php
<?php

class MyMapper extends Mapper
{
    //在setting方法中定义不同的规则，以下用$raw表示原始数据
    protected function setting()
    {
        //定义一个映射规则，
        $this->setRule('my_rule', [
            //must模式，若原字段不存在，则调用配置项中的缺省值
            'id: must' => 'user.id', //强制将$raw['id']映射为$raw['user']['id']
            'pwd: must' => 'user.pwd', //强制将$raw['pwd']映射为$raw['user']['pwd']

            //should模式，字段存在才映射
            'email: should' => 'user.email', //$raw['email']存在时，映射为$raw['user']['email']

            //may模式，字段存在且非空才映射
            'info: may' => 'user.info',//$raw['info']存在且非空时，映射为$raw['user']['info']

            //映射为多个字段
            'imgs.pic' => 'img, pic', //将$raw['imgs']['pic']映射为$raw['img']和$raw['pic']
        ]);

        //同理可定义多个不同规则
        ...
    }
}
~~~

##### 使用规则
~~~php
<?php
$Mapper = new MyMapper;

//保留所有的数据，包括未映射的数据
$data = $Mapper->withRule('my_rule')->map($raw); //$data包含原规则中不存在的字段

//只保留映射的数据
$data = $Mapper->withRule('my_rule')->map($raw, true); //$data只含有规则中定义的字段

//可同时使用多个规则，用 ',' 隔开
$Mapper->withRule('my_rule1, my_rule2')->map($raw);
~~~


---


### API

#### 列表
~~~php
final protected function setRule(string $name, array $rules): bool
final public function withRule(string $names): object
final public function map(array $raw, bool $onlyMapped = false): array
~~~

#### 详细说明

**setRule()**: 定义规则，只可在`setting()`中使用。规则格式见上述。
```php
params:
    string $name  规则名
    array  $rules 规则数组，键名为原数据中的字段名和映射模式，键值为目标映射字段，多个目标映射字段用 ',' 隔开，深层字段使用 '.' 访问。
return
    bool
```

**withRule()**: 指定当前映射使用的规则，一次性使用。映射完后，下一次映射需重新指定。
```php
params:
    string $names 本次映射使用的规则名，多个规则使用 ',' 隔开
return
    $this
```

**map()**: 映射数据，未指定规则时则返回原数据。
```php
params:
    array $raw              待映射的数据
    bool  $onlyMapped=false 是否只保留映射处理过的数据，否则包含原始未映射的数据，默认为否
return
    array 映射后的数据，未指定规则时返回原数据
```