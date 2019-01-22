目录结构
----

初始目录主要包括如下三部分：

* `app/`：应用开发目录，存放所有的应用开发文件。
* `public/`：入口文件和对外访问目录。为安全起见，应在生产环境下将其设为web根目录。
* `vendor/`：组件目录。原始组件包括composer、basement、Lin组件三套。

---


### 目录详解

初始的具体结构如下：
~~~
lin
│
├─app                               应用目录
│  │
│  ├─affix                          摆件目录
│  │  ├─event                       事件目录
│  │  ├─lang                        语言包目录
│  │  ├─response                    响应目录
│  │  │  ├─jsonxml                  json和xml的模版目录
│  │  │  └─view                     视图页面目录
│  │  │
│  │  └─route                       路由规则目录
│  │
│  ├─block                          块目录
│  │  ├─formatter                   数据格式化器目录
│  │  ├─mapper                      数据映射器目录
│  │  ├─model                       数据模型目录
│  │  └─validator                   数据验证器目录
│  │
│  ├─config                         配置目录
│  │  ├─lin-servers.php             服务器配置
│  │  ├─lin-servers.production.php  服务器配置（生产环境）
│  │  ├─lin.php                     组件配置
│  │  └─lin.production.php          组件配置（生产环境）
│  │
│  ├─layer                          层目录
│  │
│  ├─lib                            库目录
│  │  └─helper.php                  lin组件的助手函数
│  │
│  ├─boot.production.php            启动文件（生产环境）
│  ├─boot.php                       启动文件
│  └─register.php                   basement组件注册文件
│
├─public                            入口根目录
│  ├─resource                       资源文件夹
│  └─index.php                      入口文件
│
├─vendor                            组件目录
│  ├─composer                       composer组件
│  ├─basement                       basement组件
│  └─lin                            Lin组件
│
~~~

#### app目录

该目录除包括[LBA](LBA.md)的三个目录以外，还包括一个用于存放用户库的目录`app/lib`，和三个php文件：启动流程（生产环境）、启动流程（开发环境）和basement组件注册。


##### affix目录
用于存放[摆件](LBA.md)，又包含四类摆件（四个目录），分别为：

* `event/`：存放定义事件的文件。
* `lang/`：存放多语言文件。
* `response/`：存放响应文件。又包含`view/`和`jsonxml/`两个子目录，分别存放视图模板、json或xml的模板。
* `route/`：存放定义路由规则的文件。

##### block目录
用于存放[块](LBA.md)，又包含四类块（四个目录），分别为：

* `formatter/`：存放定义[数据格式化类](../components/processor/Formatter.md)的文件。
* `mapper/`：存放定义[数据映射类](../components/processor/Mapper.md)的文件。
* `model/`：存放定义[数据模型类](../components/orm/Model.md)的文件。
* `validator/`：存放定义[数据校验类](../components/validator/Validator.md)的文件。

##### layer目录
用于存放[层](LBA.md)，默认没有目录，若需结构化层，请自行定义目录。包括两个默认的文件，定义了两个默认的层：

* `Index`：响应首页的控制器。
* `Error`：响应非法地址的控制器，如`404`、`302`、`301`等响应状态。


##### config目录
用于存放配置文件，默认的配置文件为生产和开发环境下的各一套，每套包含：

* `lin`： Lin组件的配置。
* `lin-servers`： Lin服务器组件需要使用的额外配置。


##### lib目录
用于存放应用开发中需要自行引入的第三方库，包含一个默认的助手文件：

* `helper.php`：用于提供Lin常用组件类的快捷函数化调用，不建议在生产环境使用。


##### 其他文件
包括两个启动文件（生产和开发环境）和一个basement组件注册文件：

* `boot.production.php`和`boot.php`：生产和开发环境下的应用流程启动文件，已内置基本的启动方案，可按开发需求自行修改。
* `register.php`：[basement](../docs_basement/README.md)组件的注册文件，默认注册的组件全部为Lin自带的组件。可按开发需求替换或扩展组件，并且不会影响应用的执行。


---


#### public目录

该目录是框架入口也是唯一对外访问的目录，应在生产环境将其设置成web根目录。包含如下：

* `resource/`：存放资源文件的目录，如js、css、image等。
* `index.php`：框架的入口文件。
* `.htaccess`：url重写规则，用于去掉url地址中`index.php`，Apache使用。
* `web.config`：url重写规则，IIS使用。
* `robots.txt`：爬虫配置。