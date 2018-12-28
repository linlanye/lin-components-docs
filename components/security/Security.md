Security类
----
namespace: `lin\security`

包含如下类：

* **lin\security\Security**

---

### 说明

提供一系列安全相关功能的整合，通过高度抽象，以**安全场景**的**建立**和**校验**为核心，整合了如登录、授权、认证、权限访问、单点登录、oauth2、防csrf、重复提交、验证码等等常见安全功能。

包含如下三要素:

* 建立场景。一个场景包括关联的三部分：场景名、场景token、场景时效。
* 校验场景。校验场景包括三部分：校验场景是否存在，场景token是否正确、场景是否过期。可任选其一校验也可相互组合校验。
* 客户端id。场景必须依附于客户端id而存在，当未指定客户端id时，系统自动生成一个临时id。
---

### 功能

* 通过客户端id建立和校验安全场景。
* 可指定客户端id进行校验、也可使用临时id进行校验。
* 提供详细的校验结果信息。
* 整合图片验证码。
* 可使用本地文件或数据库进行场景数据管理。
* 通过安全场景的相互组合，可生成常见的安全应用模式。


#### 配置项

动态配置

~~~php
<?php
'security'  => [
    'use'     => 'local', //使用的服务器来管理安全数据
    'server'  => [
        'local' => [ //使用本地存储
            'path' => 'path/to/security', //存放路径
        ],
        'sql'   => [ //使用sql服务器
            'table'  => 'security', //表名
            'fields' => [ //键值为数据库字段名,
                'id'      => 'client_id', //最少char(70),主键
                'type'    => 'type', //客户端类型，临时或正式，最少1字节int
                'content' => 'content', //可空text类型，数据增量无上限，有效场景越多越大，
                'time'    => 'created_time', //客户端创建时间，最少4字节int
            ],
        ],
    ],
    'gc'      => [ //对临时客户端执行垃圾回收
        'probability' => 0.0001, //触发概率
        'max_life'    => 1800, //临时客户端的静止生命期(s)，同session机制
    ],
    'default' => [
        'cost' => 4, //加密token的消耗等级，越高越安全，见password_hash函数
        'life' => 1200, //默认场景时效，单位s
    ],
    'image'   => [ //图片专用(即图片验证码)
        'resolution' => [130, 50], //生成的图片分辨率，宽和高
        'length'     => 5, //验证码长度，最短1位，最长20位
        'level'      => 2, //复杂等级，最高级6，最低级0，等级越高越耗资源
        'seed'       => '23456789ABCDEFGHJKLMNPQRSTUWXYZ', //生成验证码的种子
        'background' => [], //背景图片(完整路径名)，若有则随机选择
        'ttf'        => [ //随机使用的ttf字体库，
            'lin-components/src/security/structure/captcha/stencil-four.ttf',
        ],
    ],
    'debug'   => true,
],

~~~

#### 使用

创建场景，总是使用静态方法开头。校验场景，总是使用实例方法。

* 对`指定客户端id`的建立和校验，适用于授权、认证、权限访问、单点登录、oauth2等典型应用

~~~php
<?php

$Security = New Security(1); //实例化客户端id为1

//系统自动生成安全场景的token，适用于权限访问、oauth2等
$access_token = Security::withID(1)->build('access', 1200); //建立一个access场景，且设置其有效期为1200s
$valid = $Security->check('access', $access_token); //校验该场景，返回是否通过
$Security->getStatus(); //获得校验结果状态码，具体见下述API

//指定安全场景的token，适用于单点登录等
Security::withID(1)->withToken(1)->build('login', 1200); //指定一个无效的token，只用于检测login场景是否存在
$valid = $Security->check('login', 1); //侧重检测login是否存在


//可通过建立不同的场景进行组合验证，以达到更为复杂的安全应用
...
~~~

* 对`临时id`的建立和校验，适用于常规登录、防csrf、重复提交等典型应用

~~~php
<?php

//实例化时未指定id则为临时id
$Security = New Security;

//系统自动生成安全场景的token，适用于防csrf、重复提交、验证码等。
$token = Security::build('csrf'); //建立一个csrf场景，有效期为配置项default.life
$valid = $Security->check('csrf', $token);

//指定安全场景的token，适用于常规登录等
Security::withToken(1)->build('login'); //指定一个无效的token，只用于检测login场景是否存在
$valid = $Security->check('login', 1); //侧重检测login是否存在

~~~

* 其他操作

~~~php

//输出验证码图片
Security::byImage()->build('captcha'); //验证码由系统自动生成
Security::byImage()->withToken('1234')->build('captcha'); //验证码为1234

//销毁场景
$Security->destroy('login'); //达到退出登录作用
~~~


#### 注意

* 场景数据都是超全局变量。
* `临时客户端id`和`指定客户端id`两种模式可以结合起来使用，并在各自模式下建立不同的场景进行不同侧重点验证，以达到更复杂的安全应用。
* `临时客户端id`由系统自动维护，存储在`cookie`中，不会与`客户端id`冲突，即使两者一样也是各管各的。


---


## API

#### 列表
~~~php
//场景校验相关
const VALID     = 0
const UNCHECKED = 1
const NONE      = 2
const FAILED    = 4
const EXPIRED   = 8
const INVALID   = 15
public static function __callStatic($method, $args)
public function __construct(string $id = null)
public function check(string $scenario, string $token): bool
public function getStatus(): int

//场景构建相关
public function build(string $scenario, int $life = null, int $cost = null):  ? string
public function destroy(string $scenario) : bool
public function withID(string $id): object
public function withToken(string $token): object
public function byImage(array $config = []): object
~~~

#### 详细说明

**属性**: 全部为校验结果的状态码
```php
const VALID     = 0; //检查通过
const UNCHECKED = 1; //未检查
const NONE      = 2; //场景不存在
const FAILED    = 4; //场景检查失败
const EXPIRED   = 8; //场景已过期
const INVALID   = 15; //场景无效，包含上诉4种情况
```

**::__callStatic()**: 通过静态化方法调用场景构建器区分与场景校验。静态开头则构建相关，否则为校验相关。
```php
params:
	string $method 构建器方法
	array  $args   构建器方法入参
return
	object 构建器实例
```

**__construct()**: 实例化未指定`id`时为校验临时客户端，否则校验指定客户端。
```php
params:
	string|null $id=null null时为临时id
```

**check()**: 校验场景。
```php
params:
	string $scenario 场景名
	string $token    待校验的token
return
	bool 校验是否通过
```

**getStatus()**: 获得校验场景后的具体状态码
```php
params:
	void
return
	int 状态码见上述属性中的描述
```
----

##### 场景构建

**build()**: 创建一个场景，并可指定该场景的有效期以及加密该场景`token`的复杂度(具体见`password_hash()`)。
```php
params:
	string $scenario  场景名
	int    $life=null 场景有效期，null时使用配置项default.life，为0则永不过期
	int    $cost=null 存储实际token时，需对其加密，表征该加密的复杂度，null时默认为default.cost
return
	string|null 创建成功返回token，失败返回null
```

**destroy()**: 销毁场景。
```php
params:
	string $scenario 场景名
return
	bool 是否销毁成功
```

**withID()**: 指定客户端`id`进行场景构建，一次性使用，若未调用本方法则使用临时客户端`id`。
```php
params:
	string $id 客户端id名
return
	$this
```

**withToken()**: 指定场景的`token`,若未调用本方法则由系统随机生成。
```php
params:
	string $token 指定场景的token
return
	$this
```

**byImage()**: 构建场景并以图片形式输出显示（图片验证码）。
```php
params:
	array $config=[] 对生成图片验证码使用额外的配置项，具体内容参见配置项 'image'
return
	$this
```