Flow类说明
----

### 功能

本类为流水化流程中，携带数据和指令的对象。通过Layer类的`Layer::flow()`方法启动流水化流程，在流程执行中的每一个方法入参皆为本类实例，且。


#### 使用

使用时，需编写继承[Layer](Layer.md)的类，定义的流程方法必须为`public`。
~~~php
<?php

class Controller extends Layer
{
    //开启流程的地方
    public function index()
    {
        //初始数据
        $data = 0;

        //流程执行
        $Flow = self::flow([
            'Controller.flow1', //执行顺序为flow1()、flow2()、flow3()
            'Controller.flow2'
            'Controller.flow3'
        ], $data);
        $Flow->data; //值为2，flow2()方法中调用了terminal()提前终止执行，见下述

        //获得执行细节
        $Flow->getDetails(); //返回[['app/layer/Controller', 'flow1'], ['app/layer/Controller', 'flow2']] 命名空间前缀来自Layer的配置项
    }

    //先执行
    public function flow1($Flow)
    {
        ... //代码
        $Flow->data; //此时为0
        $Flow->data = 1;
        ... //代码
    }
    //再执行
    public function flow2($Flow)
    {
        ... //代码
        $Flow->data; //此时为1
        $Flow->data = 2;
        $Flow->terminal(); //设置提前终止标记
        ... //其余代码，继续执行完
    }
    //flow2已设置终止，则此方法不再执行
    public function flow3($Flow)
    {
        ... //代码
        $Flow->data = 3;
        ... //代码
    }
}



~~~


---


## API

#### 列表
~~~php
public $data;
public function terminal(): void
public function getDetails(): array
public function isTerminal(): bool
public function reset(): bool
~~~

#### 详细说明

**属性**: 只包含一个数据属性
```
public $data; 实例携带的数据
```

**terminal()**: 用于设置提前终止执行的标记，使得后续流程不再执行
```php
params:
    void
```

**getDetails()**: 获得已执行的明细
```php
params:
    void
return
    array 格式形如[['类名', '方法名'], ...]
```

**isTerminal()**: 当前执行是否已为终点
```php
params:
    void
return
    bool 是否已为终点
```

**reset()**: 重置但不清除实例携带数据，使实例可以重复执行流程
```php
params:
    void
return
    bool 是否已为终点
```