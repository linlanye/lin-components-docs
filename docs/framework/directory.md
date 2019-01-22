## 目录结构

初始主要结构如下：
~~~
your_app
│
├─app                               应用目录
│  │
│  ├─affix                          摆件目录
│  │  ├─event                       事件目录
│  │  ├─lang                          语言包目录
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
│  │  ├─lin-servers.php              服务器配置
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
│  └─lin                            lin组件
│
~~~