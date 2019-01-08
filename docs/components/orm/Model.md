Model类
----
namespace: `lin\orm\model`

包含如下类：

* **lin\orm\model\Model**

---

### 说明
本类为关系数据模型，除包含[SQL语句构建](SQLCreator.md)的相关方法外，还包括操作数据对象的基本功能。

---

### 功能

* 构建任意用于增删改查的SQL语句并执行。（[点击查看](SQLCreator.md)）
* 模型的数据对象化和数组化操作互通。如`$Model['id']等价于$Model->id`、`$Model[0]['id']等价于$Model->{0}->id`。
* 自定义格式化器，用于入库和出库时的数据处理。
* 自定义宏，用于简化操作和映射操作。如将多个连贯操作映射为单个方法调用或将删除映射为软删除等。
* 简化关联模型操作。
* 通过宏，关联操作可实三种写入方式混搭，如更新+插入。
* 提供`setting()`方法。

#### 注意
* 需要继承后使用。
* 读操作统一使用静态开头进行访问，如`Model::where('id', 1)->select()`。
* 读操作会自动补上主键字段。
* 写操作统一使用实例进行访问，如`$Model->update()`。
* 更新无脏数据概念，只要调用便执行数据库更新；删除会剔除主键数据；插入会补上主键数据。
* 定义宏用于覆写已有方法时，读、写方法不可相互混淆。

#### 配置项

静态配置

~~~php
<?php
'orm'       => [
    'model' => [
        'default'  => [
            'table' => function ($className, $namespace) {
                return strtolower($className); //默认表名，可使用任何回调，入参为类名(不含命名空间)和所在命名空间
            },
            'pk'    => function ($className, $namespace) {
                return rtrim(strtolower($className), 's') . '_id'; //默认主键名。如模型为Users，则pk为user_id
            },
        ],
        'relation' => [
            'namespace' => 'app\\block\\model', //关联模型所处的命名空间
        ],
    ],
]
~~~


#### 使用

* 设置参数

~~~php
<?php

//需继承后通过编写类及其方法使用
class MyModel extends Model
{
    //基本参数
    protected function setting()
    {
        //设置主键和表名
        $this->setPK('user_id')->setTable('users');

        //设置多主键
        $this->setPK('user_id, order_id');

        //设置格式化器
        $this->setFormatter('read', function ($Model) {
            $Model['content'] = json_decode($Model['content']);
            return $Model; //定义一个格式化器read，将模型的content字段解码。
        });

        //设置宏，可实现简化操作和映射操作
        $this->setMacro('fastSelect', function($Map){
            return $Map->fields('id, content')->where('status: >', 0)->withFormatter('read')->select(); //将字段+条件+使用格式化器+多记录查询简化为fastSelect方法
        });
        $this->setMacro('delete', function($Map){
            return $Map->update('status', 0); //将原有的delete方法映射为更新，实现软删除
        });

        //设置关联模型，见关联模型说明
    }
~~~

* 模型读取
~~~php
<?php

//按主键查找
MyModel::find(1); //等价于MyModel::where('user_id', 1)->one();
MyModel::find(1, 1); //等价于MyModel::where(['user_id'=>1, 'order_id'=>1])->one();

//注意：one方法返回一个记录(关联数组)，select方法返回多个记录（索引数组）
$Model = MyModel::one('user_id');
$Models = MyModel::limit(1)->select('user_id'); //形如[['字段'=>'值']]

//将模型数据转为数组
$Model->toArray(); //['user_id'=>1]
$Models->toArray(); //[['user_id'=>1]]

//使用宏，使用时无需显示指定，取消使用时需指定
$Model->delete(); //自动使用上述定义的delete宏，此时等价于$Model->update('status', 0)
$Model->withoutMacro('delete')->delete(); //取消宏delete，使用原有delete方法

//使用格式化器，需显示指定
MyModel::withFormatter('read')->select('content');

//取消多个宏或使用多个格式化器使用 ',' 隔开
$Model->withoutMacro('macro1, macro2')->withFormatter('formatter1, formatter2');

//判断是否为多记录
$Model->isMulti();
~~~

* 模型写入

~~~php
<?php

//多个记录写入时，默认任一记录写入成功则成功，若需任一记录失败都失败，则使用严格模式
$Models->setStrictTrans()->update();

//模型写入失败时，会自动恢复写入前的数据

//所有写操作都只返回影响记录数

//缺少主键数据时，更新和删除会抛出异常
$Model = new MyModel;
$Model->update(); //此时抛出异常
~~~

* 关联模型
    * 只考虑主从关系，简化为主模型的关联主字段**（master key）**和从模型的关联从字段**（slave key）**，并且任何时候都只关心主模型。
    * 单向定义，如`用户`-`订单`关系，只需定义`用户`到`订单`的关系即可，无需定义`订单`到`用户`的关系。
    * 关联操作分为`select`、`insert`、`update`、`delete`四种类型，可以定义各自的映射操作，并且互不影响。
    * 对复杂关联，只需对相应的操作进行映射。如一对一查询，则定义`select`即可。
    * 定义关联模型只需调用`setReltion()`编写相应参数即可。
~~~php
<?php

//定义
class Users extends Model
{
    protected function setting(){

        //定义一个简单关系
        $this->setRelation('order', [
            'class' => 'Orders', //关联的从模型类名
            'mk' =>'order_id', //主模型（Users）的关联主字段
            'sk' =>'id', //从模型（Orders）的关联从字段
        ]);

        //定义一个简单的一对一查询关系
        $this->setRelation('order', [
            ...

            //注意：从模型的每一条记录查询都会执行下面的定义，所以需指定where条件
            //闭包入参为RMap对象和主模型的一条记录的关联主字段值
            'select' =>function($Map, $mks){
                return $Map->where('sk', $mks['order_id'])->one();
            }
        ]);

        //定义一个复杂的远程一对多查询关系
        $this->setRelation('order', [
            ...
            'select' =>function($Map, $mks){
                return $Map->join('user_order', 'user_order.user_id=user.id')->where('sk', $mks['order_id'])->one();
            }
        ]);

        //对insert、update、delete类似
        $this->setRelation('order', [
            ...
            'insert' => function($Map, $mks){
                return $Map->withData('status', 1)->insert(); //对每个从模型记录插入时添加status字段数据
            },
            'update' => function($Map, $mks){ ... }
            'delete' => function($Map, $mks){ ... }
        ]);

        //还可以定义多层嵌套关联查询
        $this->setRelation('order', [
            ...
            'select' => function($Map, $mks){
                return $Map->withRelation('goods')->one(); //对每一个从模型记录执行一个名为goods的关联查询
            }
        ]
    }
}

//使用
$Users = Users::withRelation('order')->select();//关联查询
$Users->withRelation('order')->update(); //关联更新
~~~


---


## API

#### 列表

~~~php
//公共方法
final public function __construct($data = [], int $flag = ArrayObject::STD_PROP_LIST | ArrayObject::ARRAY_AS_PROPS)
final public static function __callStatic($method, $args)
final public function __call($method, $args)
final public function toArray(): array
final public function getParent():  ? object
final public function isMulti() : bool

//设置参数方法
final protected function setTable(string $table): object
final protected function setPK(string $pk): object
final protected function setMacro(string $macro, Closure $Closure): object
final protected function setFormatter(string $formatter, Closure $Closure): object
final protected function setRelation(string $relation, array $params = []): object
~~~


#### 详细说明

**__construct()**: 模型继承自**php**内置的`ArrayObject`对象，实现数组和对象互操作。
```php
params:
    array|object $data=[] 模型的数据来源，可为关联数组（单记录），元素为关联数组的索引数组（多记录），模型对象或std对象，或这几者的混合。
    int $flag = ArrayObject::STD_PROP_LIST | ArrayObject::ARRAY_AS_PROPS 参见ArrayObject说明
```

**__callStatic()**: 动态调用读操作方法，由专用的Map对象提供，所有读操作皆必须使用静态方法开头
```php
params:
    string $method 调用的Map方法
    array  $args   Map方法参数
return
    object 一个RMap（读取专用）对象实例，该对象用于将读操作映射为sql语句并执行
```

**__call()**: 动态调用写操作方法，由专用的Map对象提供，所有写操作只能由Model对象实例调用
```php
params:
    string $method 调用的Map方法
    array  $args   Map方法参数
return
    object 一个WMap（写入专用）对象实例，该对象用于将写操作映射为sql语句并执行
```

**toArray()**: 将模型数据转为数组
```php
params:
    void
return
    array 包含模型数据的数组
```

**getParent()**: 获得当前模型的父（主）模型，用于关联操作的主从关系，从模型调用该方法则获得主模型。
```php
params:
    void
return
    object 主模型
```

**isMulti()**: 当前模型是否为多记录
```php
params:
    void
return
    bool 由one和find方法查询的结果为单记录，select方法查询的为多记录。传入数据并实例化时，若数据为非空索引数组则判断为多记录，否则为单记录。
```

**setTable()**: 设置当前模型表名，若无则调用配置项中的默认执行
```php
params:
    string $table
return
    $this
```

**setPK()**: 设置当前模型主键名，多个主键用 ',' 隔开
```php
params:
    string $pks
return
    $this
```

**setMacro()**: 设置当前模型的宏。注：宏不可嵌套，即定义宏的闭包中不可以出现其他宏
```php
params:
    string  $macro   宏名
    Closure $Closure 执行宏的闭包，闭包的入参为读或写对应的Map对象，闭包的返回则为宏的返回
return
    $this
```

**setFormatter()**: 设置当前模型的格式化器，用于在出入库时自动对数据进行处理。注：多记录情况下始终以一个单记录进行遍历处理
```php
params:
    string  $formatter 格式化器名
    Closure $Closure   执行格式化的闭包，闭包的入参为模型的每一个记录的原始数据，该数据为关联数组形式。注：闭包的返回应为一个处理好后的记录
return
    $this
```

**setRelation()**: 设置当前模型的关联模型，关联模型只定义单向的主从关系，如`用户-订单`关联，只需在`Users`模型定义关联`Orders`即可。至于是一对多或者远程关联等，皆通过对应的读写方法映射实现。
```php
params:
    string $relation 关联模型名，用于映射为模型的属性。
    array  $params   设置关联模型的参数，详细如下
return
    $this

$params = [
    'class' => string。目标关联模型的类名，则默认使用$relation作为类名。（从模型）
    'mk'     => string。当前模型用于关联的键名，多个情况下使用 ","隔开；默认使用当前模型主键。（主模型）
    'sk'     => string。目标模型用于关联的键名，多个情况下使用 ","隔开；默认使用目标模型主键。（从模型）
    'merge' => bool。关联查询时，是否合并从模型数据到主模型，默认false。（主模型）

    //自定义对从模型的读写操作，该定义的操作不与宏冲突，并且只作用于对从模型的操作，默认无。
    'select' => Closure|mixed。对从模型的所有读操作进行映射（包括one、select、find），入参为RMap对象和主模型的mk值。闭包的返回为从模型的值。使用该参数，可轻松实现n对n关联，远程关联，多级嵌套关联等复杂的关联模型。当值不为闭包时，则返回该值作为从模型的值。
    'insert' => 同上。
    'update' => 同上。
    'delete' => 同上。
];
```

---


#### Map对象方法
用于为模型提供读写操作。
注意：`with`开头的方法，都只能一次性使用，下一次使用需继续显示调用。

~~~php
<?php

// 使用指定驱动,需满足basement::ServerSQL
public function setDriver(object $Driver): object

//获得指定驱动
public function getDriver(): object

//本次操作使用关联模型，多个情况下使用 ',' 隔开
public function withRelation(string $relations): object

//本次操作使用格式化器，多个情况下使用 ',' 隔开
public function withFormatter(string $formatters): object

//本次操作不使用宏，多个情况下使用 ',' 隔开
public function withoutMacro(string $macros): object

//读取专用，快速获得指定主键的记录
public function find($pk, ...$pks): object

//写专用，设置多记录写入时为严格模式，此时任一记录操作失败皆失败。默认为宽松模式，任一记录操作成功皆成功。
public function setStrictTrans(): object

//SQL语句构建部分参考SQLCreator说明
~~~