Formatter类内置格式化函数
---

#### 列表

~~~php
<?php

/****任意类型转基础类型****/
public function toInt($v): int
public function toFloat($v): float
public function toString($v): string
public function toBool($v): bool
public function toArray($v): array
public function toObject($v): object

/****任意类型转指定类型****/
public function toDate($v, ? string $default = null):  ? string //转为标准日期
public function toActive($v, ? int $default = 0) :  ? int //转为1或默认值
public function toPNum($v, ? int $default = null) :  ? int //转为正整数
public function toNatNum($v, ? int $default = null) :  ? int //转为自然数
public function toNNum($v, ? int $default = null) :  ? int //转为负整数
public function toPrice($v, ? string $default = null) :  ? string //转为两位小数字符串
public function toTString($v, ? string $default = null) :  ? string //转换为千分位分割的数字
public function toPast($t, ? string $default = null) :  ? string //转为过去时间
public function toFuture($t, ? string $default = null) :  ? string //时间戳转为未来时间
public function toCountdown($v, ? string $default = null) :  ? string //转为倒计时

/***指定类型转另一类指定类型***/
public function num2IP($v, ? string $default = null) :  ? string //整数转IP
public function ip2Num(string $v, ? int $default = null) :  ? int //IP转整数
public function date2time($v, ? int $default = null) :  ? int //日期转时间戳

/*********字符专用转换*********/
public function forPwd(string $v, int $cost = 10) : string //明文字符串转安全密码
public function forHTML(string $v): string //对html字符进行转义
public function stripSymbol(string $v, string $symbol): string //剔除字符串中的指定符号
public function stripComma(string $v): string //剔除字符串中的所有逗号
public function stripSpace(string $v): string //剔除字符串中的所有空格
~~~

---

### 详细说明

所有带$default入参的函数，皆在待格式化的数据无效时用作默认返回。

#### 任意类型转基础类型

**toInt()**: 转为整形，当数据为`object`时，先获得其属性值再转整形。
```php
params:
    mixed $v 数据
return
    int
```

**toFloat()**: 转为浮点型，当数据为`object`时，先获得其公共属性值再转。
```php
params:
    mixed $v 数据
return
    float
```

**toString()**: 转为字符型，当数据为`array或object`时转为序列化值，当为`resource`时先转为整形再转。
```php
params:
    mixed $v 数据
return
    string
```

**toBool()**: 转为布尔型
```php
params:
    mixed $v 数据
return
    bool
```

**toArray()**: 转为数组，当数据为`object`时，先获得其公共属性值再转。
```php
params:
    mixed $v 数据
return
    array
```

**toObject()**: 转为对象
```php
params:
    mixed $v 数据
return
    object
```
#### 任意类型转指定类型

**toDate()**: 转为标准日期
```php
params:
    mixed $v 数据
return
    object
```

**toDate()**: 转为标准日期
```php
params:
    mixed  $v            数据
    string $default=null 转换无效时候返回的默认值
return
    string|null 转换后的格式为 'Y-m-d H:i:s'
```

**toActive()**: 转为0或1，为数字时大于0转为1，其余转为0；当为字符'yes', 'on', 'accept', 'accepted', 'agree'时转为1，其余情况返回`$default`值
```php
params:
    mixed $v         数据
    int   $default=0 转换无效时候返回的默认值
return
    string|null
```

**toPNum()**: 转为正整数，大于0转为整数，否则转为1，非数字时返回`$default`值
```php
params:
    mixed $v            数据
    int   $default=null 转换无效时候返回的默认值
return
    int|null
```

**toNatNum()**: 转为自然数，大等于0转为整数，否则转为0，非数字时返回`$default`值
```php
params:
    mixed $v            数据
    int   $default=null 转换无效时候返回的默认值
return
    int|null
```

**toNNum()**: 转为负整数，小于0转为整数，否则转为-1，非数字时返回`$default`值
```php
params:
    mixed $v            数据
    int   $default=null 转换无效时候返回的默认值
return
    int|null
```

**toPrice()**: 转为两位小数字符串，非数字时返回`$default`值
```php
params:
    mixed  $v            数据
    string $default=null 转换无效时候返回的默认值
return
    string|null
```

**toTString()**: 转换为千分位分割的数字，非数字时返回`$default`值
```php
params:
    mixed  $v            数据
    string $default=null 转换无效时候返回的默认值
return
    string|null
```

**toPast()**: 转为过去时间，非数字时返回`$default`值
```php
params:
    mixed  $v            数据
    string $default=null 转换无效时候返回的默认值
return
    string|null 格式形如：x年前、x个月前、x天前、x小时前、x分钟前、x秒前、现在
```

**toFuture()**: 时间戳转为未来时间，非数字时返回`$default`值
```php
params:
    mixed  $v            数据
    string $default=null 转换无效时候返回的默认值
return
    string|null 格式形如：x年后、x个月后、x天后、x小时后、x分钟后、x秒后、现在
```

**toFuture()**: 转为倒计时，非数字时返回`$default`值
```php
params:
    mixed  $v            数据
    string $default=null 转换无效时候返回的默认值
return
    string|null 格式形如：x天x时x分x秒
```

#### 任意类型转指定类型

**num2IP()**: 整数转IP，非数字或超过`+-0x7fffffff`时返回`$default`值
```php
params:
    mixed  $v            数据
    string $default=null 转换无效时候返回的默认值
return
    string|null
```

**ip2Num()**: IP转整数，非有效ip或转换失败时返回`$default`值
```php
params:
    mixed $v            数据
    int   $default=null 转换无效时候返回的默认值
return
    int|null
```

**date2Timestamp()**: 日期转时间戳，非日期字符或数字时返回`$default`值
```php
params:
    mixed $v            数据
    int   $default=null 转换无效时候返回的默认值
return
    int|null
```

#### 字符专用转换

**forPwd()**: 明文字符串转安全密码
```php
params:
    string $v       数据
    int    $cost=10 计算耗费，越高越安全。见password_hash()说明
return
    string
```

**forHTML()**: 对html字符进行转义
```php
params:
    string $v 数据
return
    string
```

**stripSymbol()**: 剔除字符串中的指定符号
```php
params:
    string $v      数据
    string $symbol 欲剔除的字符
return
    string
```

**stripComma()**: 剔除字符串中的所有逗号
```php
params:
    string $v 数据
return
    string
```

**stripSpace()**: 剔除字符串中的所有空格
```php
params:
    string $v 数据
return
    string
```