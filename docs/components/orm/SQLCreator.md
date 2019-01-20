SQLCreator
----

### 说明
用于给[Model类](Model.md)和[Query类](Query.md)类提供sql语句构建的功能，包含一系列sql语句操作方法，不能单独使用，只能集成在[Model类](Model.md)和[Query类](Query.md)中使用。每一个语句都严格使用了变量绑定。

---

### 功能

* 增删改查相关的各类语句生成。
* 各类复杂子句的生成。



#### 配置项

动态配置

~~~php
<?php

'orm'       => [
    'page'  => [
        'number' => 20, //分页时一次查询的条数
    ],
],
~~~

#### 使用
注：以下示例使用的变量名（$Creator）均可代表Model实例或Query实例

##### 读操作
~~~php
<?php

//使用select()和one()
$Creator->table('users')->select(); //sql: select * from users
$Creator->table('users')->one(); //sql: select * from users limit 1

//可传入字段
$Creator->table('users')->select('id, pwd'); //sql: select id, pwd from users
$Creator->table('users')->fields('id, pwd')->select(); //和上述语句等价

//子语句查询，同理可使用闭包嵌套更多层的子语句
$Creator->table(function($Creator){
    $Creator->table('users')->select();
})->select(); //sql: select * from (select * from users) as lin0

//聚合查询，除下述外，还包括min()、avg()、sum()方法
$Creator->table('users')->count(); //sql: select count(*) from users
$Creator->table('users')->max('id'); //sql: select max(id) from users

//集合操作，除下述外，还包括except，intersect
$Creator->table('users')->union()->table('orders')->select(); //sql: select * from users union select * from orders

//group、order、limit语句
$Creator->table('orders')->fields('user_id, order_id')->group('user_id')->order('order_id asc')->limit(10); //sql: select user_id, order_id from orders group by order_id order by order_id asc limit 10
~~~


##### 条件语句（**where**和**having**）

条件语句由三部分构成，形如：['字段:操作符' => '值']，单个条件可以简化为`where('字段:操作符', '值')`，以下示例使用`where()`代表，`having()`与之相同。

* 默认操作符 `=`
~~~php
<?php

//单个条件可使用两个标量参数
$Creator->where('id', 1); //sql: where id=1

//使用多个条件，使用一个数组参数
$Creator->where(['id' => 1, 'status' => 1);//sql: where id=1 and status=1
~~~

* 显示指定操作符
	* 比较操作符：`<、<=、>、>=、=、!=、<>` 或 `lt、le、gt、ge、eq、neq`。
	* 其它：`like、notLike`，`in、notIn`，`between、notBetween`，`exists、notExists`，`isNull、notNull`。
~~~php
<?php

//比较操作符
$Creator->where('status: >', 0); //where status>0

//like及notLike
$Creator->where('name: like', 'lin'); //sql: where name like lin

//in及notIN，值应为数组
$Creator->where('id: in', [1, 2]); //sql：where id in (1, 2)

//exists及notExists，适用子语句或任意标量值
$Creator->where('exists', function($Creator){
    $Creator->table('users')->select();
}); //sql: where exists (select * from users)

//between及notBetween，值应为数组
$Creator->where('id: between', [1, 3]); //sql：where id between (1, 3)

//isNull及notNull，无需任何值
$Creator->where('status: isNull'); //sql：where status is null
~~~

* 多条件连接。所有条件默认为**and**连接，若需**or**方式，则需显示指定。
	* 每一个`or`需是条件数组中的一个元素，并且其左右元素都为一个条件规则。
	* 条件数组中每加一个`[]`等效于加一个sql语句中加一个`()`。

~~~php
<?php

//多条件or
$Creator->where(['id' => 1, 'or', 'status: notNull']); //sql: where id=1 or status is not null

//更复杂的多条件连接，每加一个[]等效于加一个()
$Creator->where([
    "f1:neq" => 1, 'or', [["f2" => 1], ["f3:gt" => 1]],
]); //where f1!=1 or ((f2=1) and (f3>1))
~~~

##### join语句

join语句由三部分构成，形如：['表名:连接方式'=>'连接条件']，单个join可简化为`join('表名:连接方式', '连接条件')`。连接方式如：`inner`、`nature`、`outer`等。
~~~php
<?php

//单个join
$Creator->table('orders')->join('users'); //sql: orders join users
$Creator->table('orders')->join('users: inner', 'users.id=orders.id'); //sql:orders inner join users on users.id=orders.id

//多个join
$Creator->table('orders')->join(['users'=>'users_id=order.id', 'goods'=>'goods_id=orders_id']); //orders join users on users_id=order.id join goods on goods_id=orders_id
~~~

##### 写操作

~~~php
<?php

//新增或更新记录的三种形式
$Creator->table('orders')->insert('id', 1); //新增id为1的记录，update相同
$Creator->table('orders')->insert(['id'=>1, 'info'=>'...']); //新增指定id和info字段值记录，update相同
$Creator->table('orders')->insert(function($Creator){
    $Creator->table('orders_users')->select('id');
}); //sql: insert into orders (select id from orders_users)

//使用withData()方法可分批次传入字段数据，以下语句相同
$Creator->table('orders')->withData(['id'=>1])->withData(['info'=>'...'])->insert();
$Creator->table('orders')->withData(['id'=>1, 'info'=>'...'])->insert();


//删除记录
$Creator->table('orders')->where('id', 1)->delete(); //sql: delete from orders where id=1
~~~

---


## API

#### 列表
~~~php

//操作终止方法
public function select(string $fields = '')
public function one(string $fields = '')
public function max(string $field)
public function min(string $field)
public function sum(string $field)
public function avg(string $field)
public function count(string $field = '*')
public function insert($data = null, $value = null): int
public function update($data = null, $value = null): int
public function replace($data = null, $value = null): int
public function delete(): int

//连贯操作，返回$this
public function union(string $type = ''): object
public function intersect(string $type = ''): object
public function except(string $type = ''): object
public function table($table): object
public function fields(string $fields): object
public function group(string $group): object
public function order(string $order): object
public function limit(int $start, int $num = 0): object
public function where($condition, $value = null): object
public function having($condition, $value = null): object
public function join($condition, string $expression = null): object
public function withData(array $data, bool $notOverride = false): object
~~~
