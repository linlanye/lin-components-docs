# Basement/1.0
[![Latest Stable Version](https://poser.pugx.org/basement/basement/v/stable)](https://packagist.org/packages/basement/basement)
[![Total Downloads](https://poser.pugx.org/basement/basement/downloads)](https://packagist.org/packages/basement/basement)
[![PHP Version](https://img.shields.io/badge/php-%3E%3D7.2-8892BF.svg)](http://www.php.net/)
[![License](https://poser.pugx.org/basement/basement/license)](https://packagist.org/packages/basement/basement)
> requires `php>=7.2` and `composer`

## 介绍

Basement的目的是让web中常用的php组件功能可以通用化使用，并且可以实现程序解耦，是由一套**trait**和一个类**Linker**共同构成。

* 这一套**trait**类似扩展了的接口，对最常用的类和方法进行了非常严格的规定，在依靠php语法无法约束的场景，会有一定的文字约束甚至逻辑代码用于提醒实现者严格按照规定实现类。用**trait**的原因是对有些方法需要用一定的代码约束，同时php是单继承，因此要避免使用继承方式对实现类的影响，所以接口和抽象类都不能满足，而**trait**则成了最优选择。
* **Linker**这个类定义在根命名空间，只有全静态的方法，是basement的核心所在。它用于注册和访问组件，注册的组件可以是上述**trait**名（也即标准组件），也可以是用户自定义的组件名。



## 目的

* 精准抽象和简单访问。大部分web开发具有大量重复的地方，对这些地方进行抽象并提供一套最简单的访问模式。
* 统一规则和屏蔽区别。用相同的需求具有不同的实现，统一这些实现接口并将其特有的调用方式屏蔽掉，使其对开发者简单化、透明化。
* 对组件的替换可以无缝进行。若某个组件出现问题，只需更改所注册的组件名。
* 通用化组件。使得不同的组件可以在不同的程序上面以相同的方式调用。


## 安装

* 1.composer方式
```
配置composer.json文件
"require": {
    "basement/basement": "1.0.*@stable"
}
或
composer require basement/basement 1.0
```

* 2.下载方式
```
下载后，require "boot.php";
```


## 使用

* 使用`Linker::register(['Config(组件名)'=>'some_config_class(具体类名)'])`注册组件，即该方式注册了一个名为**Config**的组件，对应的类为**some_config_class**。
* 使用`Linker::Config()`获得**some_config_class**类名，或使用`Linker::Config(true)`获得**some_config_class**实例。即使用**Linker**动态调用的静态方法皆为注册的组件名，形式为Linker::组件名()。


## 注意

##### 调用注意
* 找不到Linker类的错误，可手动加载`boot.php`文件。
* 组件名不区分大小写。

##### 实现注意
* 所有函数接口返回的内容应该是独立的，不受环境或配置影响。
* 所有方法除限定返回布尔变量的外，失败或错误或无等情况都必须返回`null`，不能用`false`代替！


## 文档
核心(class)

* [Linker](docs/Linker.md)

标准组件(trait)

* [Config](docs/Config.md)
* [Debug](docs/Debug.md)
* [Event](docs/Event.md)
* [Exception](docs/Exception.md)
* [Lang](docs/Lang.md)
* [Log](docs/Log.md)
* [Request](docs/Request.md)
* [ServerFile](docs/ServerFile.md)
* [ServerKV](docs/ServerKV.md)
* [ServerLocal](docs/ServerLocal.md)
* [ServerQueue](docs/ServerQueue.md)
* [ServerSQL](docs/ServerSQL.md)


## 组件开发

开发basement标准组件完成后，除了Server系列组件外，都需通过basement的单元测试，拷贝basement-phpunit.xml文件到项目根目录，配置待测试的内容即可。
Server系列组件由于牵扯数据交互，由开发者自行测试。


## 捐赠
![捐赠林澜叶](https://img.lin-php.com/donations.png)

## 版权信息
* 作者：林澜叶(linlanye)
* 开源协议：[MIT LICENSE](LICENSE)

