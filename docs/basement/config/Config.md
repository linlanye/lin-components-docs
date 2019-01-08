Config类
----
namespace: `lin\basement\config`

包含如下类：

* **lin\basement\config\Config**

---

### 功能

* basement部分，[点击查看](https://github.com/linlanye/basement)
* 扩展功能：
    * 动态加载，并可使用 `.` 对配置项进行多级读写。
    * 清空指定配置文件。
    * 重置所有配置。



#### 配置项

静态配置

~~~php
<?php

'config'    => [
    'path'  => 'configs/path', //存放配置文件的目录
    'debug' => true, //调试模式
],
~~~

#### 使用

~~~php
<?php

//动态读取
Config::lin('k0.k1'); //等效于Config::get('lin')['k0']['k1']

//动态设置
Config::lin(['k0.k1'=>'v']); //将lin配置中的['k0']['k1']项设置为v值

//清除目标配置数据
Config::clean('lin');//清空lin的配置
Config::clean();//清空所有配置

//重置
Config::reset();
~~~


---


## API

#### 列表
~~~php
//非basement部分
public static function __callStatic($name, $arg)
public static function clean(string $name = ''): bool
public static function reset(): bool
~~~

#### 详细说明
**::__callStatic()**: 动态读写配置
```php
params:
    string $name 配置名
    array  $arg  只取arg的首个元素，若为字符串，则读取配置，若为数组则写入配置，支持使用 “.” 多级调用。
return:
    array|null 失败或不存在则返回null
```

**::clean()**: 清除
```php
params:
    string $name='' 配置名，空则清空所有
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
