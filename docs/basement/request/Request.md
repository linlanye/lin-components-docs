Request类
----
namespace: `lin\basement\request`

包含如下类：

* **lin\basement\request\Request**

---

### 功能

* basement部分，[点击查看](https://github.com/linlanye/basement)
* 扩展功能：
    * $_GET、 $_POST和类方法绑定，通过任何一个修改，另一个都发生变化。
    * 映射类属性为当前请求参数。
    * 模拟任何类型的请求。
    * 使用 `.` 对参数的多级读写。
    * 获取有效的上传数据。
    * 获取上传错误信息。
    * 清空指定方法的数据。
    * 重置并清空所有寒暑假。


#### 配置项

静态配置

~~~php
<?php

'request'   => [
    'type'    => [ //自定义请求类型
        'tag'        => '__method', //标示自定义类型的键名，值为自定义类型。如表单中name为:__method,value为PUT，则请求类型为PUT
        'substitute' => 'POST', //用于模拟自定义请求的类型，在该请求类型下自定义类型才会生效
    ],
    'uploads' => [
        'path'   => 'path/to/uploads', //上传文件存放路径
        'rename' => function ($rawFile) {}, //可使用回调对上传文件重命名，入参为原文件名
        'filter' => function ($fileType, $fileSize) {//入参为文件后缀名，文件大小（来自$_FILES）
            return true; //对上传文件进行过滤，返回false则滤掉
        },
    ],
],
~~~

#### 使用

~~~php
<?php

//实例属性 = 当前请求参数。假设当前请求方法为GET
$Requert = new Requert;
$Requert->id; //等价于$_GET['id']
$Requert->id = 'value'; //等价于$_GET['id'] = 'value'

//模拟任意请求。配置项如上，假设表单post的数据为['__method'=>'PUT', 'id'=>'value' ],
Request::getMethod(); //值为PUT
Request::getRawMethod(); //值为POST
Request::getCurrent(); //值为['id'=>'value']
$_POST; //值为空

//参数的多级读写
$Request->params('post.id'); //等价于$_POST['id']
$Request->params('post'); //等价于$_POST
$Request->params('*'); //获取所有参数['POST'=>'', 'GET'=>'', ...]
$Request->params(''); //入参为空，返回null
$Request->params(['post.id' => 'value']); //等价于$_POST['id'] = 'value'

//获取有效的上传数据，数据来源于 $_FILES 和配置项 'uploads' 的共同作用
$uploads = Request::getUploads();
$uploads = [
    'file1' => [
        ['name' => '重命名后的名字', 'type' => '文件类型', 'size' => '文件大小',
        'file' => '包含完整路径',],
        ...
    ],
    ...
];//uploads形式


//获取上传错误信息
$errors = Request::getUploadsError();
$errors = [
    'file1' => [
        ['错误码, 见下述']
    ],
    ...
];//errors形式

//清除目标方法数据
Request::clean('get');//清空GET方法的参数
Request::clean();//清空所有方法参数

//重置
Request::reset();
~~~


---


## API

#### 列表
~~~php
//非basement部分
const UPLOAD_OK                = UPLOAD_ERR_OK;
const UPLOAD_INI_SIZE          = UPLOAD_ERR_INI_SIZE;
const UPLOAD_FORM_SIZE         = UPLOAD_ERR_FORM_SIZE;
const UPLOAD_PARTIAL           = UPLOAD_ERR_PARTIAL;
const UPLOAD_NO_FILE           = UPLOAD_ERR_NO_FILE;
const UPLOAD_NO_TMP_DIR        = UPLOAD_ERR_NO_TMP_DIR;
const UPLOAD_CANT_WTITE        = UPLOAD_ERR_CANT_WRITE;
const UPLOAD_EXTENSION         = UPLOAD_ERR_EXTENSION;
const UPLOAD_NOT_UPLOADED_FILE = 9;
const UPLOAD_INVALID           = 10;
const UPLOAD_CANT_MOVE         = 11;
public function __get(string $param)
public function __set(string $param, $value) : bool
public function __isset(string $param) : bool
public function params($stringOrArray = '*')
public static function getUploads():  ? array
public static function getUploadsError(): array
public static function getRawMethod():  ? string
public static function clean(string $method = '') : bool
public static function reset(): bool
~~~

#### 详细说明

**属性**: 以下常量值，用于上传错误时候的错误码
```php
const UPLOAD_OK                = UPLOAD_ERR_OK; //见php对应常量
const UPLOAD_INI_SIZE          = UPLOAD_ERR_INI_SIZE; //见php对应常量
const UPLOAD_FORM_SIZE         = UPLOAD_ERR_FORM_SIZE; //见php对应常量
const UPLOAD_PARTIAL           = UPLOAD_ERR_PARTIAL; //见php对应常量
const UPLOAD_NO_FILE           = UPLOAD_ERR_NO_FILE; //见php对应常量
const UPLOAD_NO_TMP_DIR        = UPLOAD_ERR_NO_TMP_DIR; //见php对应常量
const UPLOAD_CANT_WTITE        = UPLOAD_ERR_CANT_WRITE; //见php对应常量
const UPLOAD_EXTENSION         = UPLOAD_ERR_EXTENSION; //见php对应常量
const UPLOAD_NOT_UPLOADED_FILE = 9; //不是上传文件
const UPLOAD_INVALID           = 10; //用户自定义校验不过关
const UPLOAD_CANT_MOVE         = 11; //无法移动文件
```


**__get()**: 通过实例属性获取当前请求方法的参数
```php
params:
    string $param 参数名
return
    mixed|null 失败返回null
```

**__set()**: 通过实例属设置当前请求方法的参数
```php
params:
    string $param 参数名
    mixed  $value 参数值
return
    bool 是否成功
```

**__isset()**: 通过isset()方法查看实例属性对应的参数是否存在
```php
params:
    string $param 参数名
return
    bool 是否存在
```

**params()**: 读写任意参数，支持使用 `.` 多级调用和 `*` 通配符
```php
params:
    string|array $stringOrArray='*'  为字符串时则读取（空返回null，*返回所有参数），数组时则写入。
return
    mixed|null|bool 设置时返回bool，读取时候返回mixed或null
```

**::getUploads()**: 获得上传文件信息，和配置项`uploads`搭配使用
```php
params:
    void
return
    array|null 失败返回null，成功格式形如['file1' => [['name' => '重命名后的名字', 'type' => '文件类型', 'size' => '文件大小','file' => '包含完整路径']]]
];
```

**::getUploadsError()**: 获得上传失败信息
```php
params:
    void
return
    array|null 失败返回null，成功格式形如['file1' => [['错误码，见上述常量属性']]]
];
```

**::getRawMethod()**: 获得原始的请求方法，非模拟方法名，区别于`getMethod()`。
```php
params:
    void
return
    string|null 原始的请求方法名，失败返回null
];
```

**::clean()**: 清除某个方法的参数
```php
params:
    string $method='' 方法名，空则清空所有
return:
    bool 是否成功
```

**::reset()**: 重置
```php
params:
    void
return:
    bool 是否成功
```