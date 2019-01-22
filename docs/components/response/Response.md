Response类
----
namespace: `lin\response`

包含如下类：

* **lin\response\Response**

---

### 说明
提供HTTP响应，包括响应页面、json、xml、下载、地址跳转等。

---

### 功能

* 显示成功、失败页面。
* 跳转前一页。
* 显示视图页面。
* 响应json、xml数据。
* 重定向。
* 提供下载或显示文件。
* 指定响应头和响应状态。



#### 配置项

动态配置

~~~php
<?php
'response'  => [
    'view'    => [//响应视图相关
        'error'        => 'lin-components/src/response/structure/error.html', //错误页面
        'success'      => 'lin-components/src/response/structure/success.html', //成功页面
        'countdown_id' => 'lin-jump-countdown', //跳转倒计时所在的html节点id
        'method'       => function ($template, $data) {
            $View = new lin\view\View; //用于嵌入view()方法的实际执行，入参为视图模板名、视图数据
            return $View->withData($data)->show($template);
        },
    ],
    'jsonxml' => [//响应json、xml相关
        'charset'      => 'utf8', //编码
        'path'         => 'path/to/jsonxml', //模板目录
        'cross_origin' => [
            'on'     => false, // 是否允许跨域
            'domain' => '*', //允许的域名列表
        ],
    ],
    'file'    => [//响应文件，显示或下载
        'path' =>  'path/to/files', //默认响应文件的存放路径
    ],
    'default' => [ //默认值
        'error'   => [
            'countdown' => 3, //跳转倒计时（单位：s），0为不跳转
            'info'      => 'Error!', //默认错误信息
        ],
        'success' => [
            'countdown' => 3,
            'info'      => 'Success!',
        ],
        'json'    => [
            'opt'      => JSON_UNESCAPED_UNICODE, //json选项设置，见json_encode第二个参数
            'depth'    => 512, //编码深度，见json_encode第三个参数
            'template' => null, //默认模板名，为空则不使用
        ],
        'xml'     => [
            'header'   => '<?xml version="1.0" encoding="utf8"?>', //xml头
            'template' => null, //默认模板名，为空则不使用
        ],
    ],
],
~~~

#### 使用

~~~php
<?php
$Res = new Response;

//页面跳转
$Res->back(); //跳转到上一页面
$Res->success('成功', 3, 'https://www.lin-php.com'); //显示成功页面并输出信息，停顿3秒后跳回指定url
$Res->error('错误'); //显示错误页面并输出信息，停顿默认秒数后跳回默认url
$Res->redirect('https://www.lin-php.com'); //立即跳转到指定url

//响应指定数据的json、xml或视图页面
$Res->withData(['stauts' => 'ok'])->json(); //使用默认模板响应json
$Res->withData(['stauts' => 'ok'])->xml('my_template'); //使用模板响应xml
$Res->withData(['stauts' => 'ok'])->view('my_view'); //响应某个视图，具体的响应由配置项view.method所执行

//响应下载或显示文件
$Res->download('my_file', 'show_name'); //响应下载，下载文件来自配置项中file.path目录下的my_file文件，并对用户显示文件名为show_name
$Res->show('my_img.jpg', 'show_name'); //向用户显示图片

//指定http响应头和状态码
$Res->withHeader('my_heard')->withStatus(200)->view();
~~~


---


### API

#### 列表
~~~php
public function withHeader($header): object
public function withStatus(int $status): object
public function withData(array $data): object
public function back(): void
public function error(string $info = '', int $countdown = null, string $url = ''): void
public function success(string $info = '', int $countdown = null, string $url = ''): void
public function redirect(string $url = '/'): void
public function json(string $template = null, int $opt = null, int $depth = null): void
public function xml(string $template = null, string $xmlHeader = null): void
public function view(string $template = null): void
public function download(string $file, string $showName = null): void
public function show(string $file, string $showName = null): void
~~~

#### 详细说明

**withHeader()**: 指定响应头
```php
params:
    array|string $header 响应头，数组时批量设置
return
    $this
```

**withStatus()**: 指定http响应状态码
```php
params:
    int $status http状态码
return
    $this
```

**withData()**: 为`json()`、`xml()`、`view()`指定响应模板数据，多次调用则合并数据。当`json()`或`xml()`未使用模板时，则直接输出数据；否则模板中将按键名为变量名，键值为变量值分配模板变量。
```php
params:
    array $data 响应数据
return
    $this
```

**back()**: 跳回上一页面
```php
params:
    void
```

**error()**: 显示一个错误页面，该页面来自配置项`view.error`，输出x信息，并停顿x秒后跳回x地址
```php
params:
    string $info=''        输出信息，为空时使用配置项 'default.error.info'
    int    $countdown=null 停顿时间，为null时使用配置项 'default.error.countdown'
    string $url=''         跳转地址，为空时返回上一个referer，若无referer则跳回主页
```

**success()**: 显示一个成功页面，见`error()`说明
```php
params:
    string $info=''        输出信息，参见 'error()' 说明
    int    $countdown=null 停顿时间，参见 'error()' 说明
    string $url=''         跳转地址，参见 'error()' 说明
```

**redirect()**: 跳转到目标地址
```php
params:
    string $url='/' 跳转地址
```

**json()**: 响应`json`数据
```php
params:
    string $template=null 响应json时使用的数据模板，空则不使用
    int    $opt=null      见json_encode() $opt说明
    int    $depth=null    见json_encode() $depth说明
```

**xml()**: 响应`xml`数据
```php
params:
    string $template=null  响应xml时使用的数据模板，空则不使用
    string $xmlHeader=null xml响应头
```

**view()**: 响应一个视图，具体的视图操作由配置项中`view.method`的回调函数所执行
```php
params:
    string $template=null 视图页面模板
```

**download()**: 将配置项目录`file.path`的某个文件提供给用户下载，并指定该文件的下载名称
```php
params:
    string $file          提供下载的原文件名
    string $showName=null 向用户显示的文件名，若空则为原文件名
```

**show()**: 自动根据文件扩展名显示文件或下载文件
```php
params:
    string $file          用于显示的原文件名，显示可为图像、文本。其他情况则转为下载
    string $showName=null 向用户显示的文件名，若空则为原文件名
```